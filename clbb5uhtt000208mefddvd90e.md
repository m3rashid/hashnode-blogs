# Creating API server in nodejs

Node.Js or Javascript in general is certainly not a very awesome programming language and not the best choice for creating high-performance server-side applications. But even then, it is quite popular in the industry, largely because of the community and having the same language on both the front end and the backend code. This has led to the development of two of the most famous and on-demand tech stacks called MERN (MongoDB, Express.Js, React.Js, Node.Js) and MEAN (same as MERN with Angular.js in place of React.Js) and many others like MEVN, MESN etc.

Let's shift our focus today to the server side only, dealing with creating APIs with Node.Js. In this tutorial, I use Express.Js as the server framework, MongoDB as the database and other things that would be discussed as the tutorial goes on.

### Motivation

The motive behind this is to give some insights into how I prefer to do certain things when creating my APIs in nodejs. This architecture is very scalable and maintainable from a developer's perspective. There may be methods better than this and used widely in the industry, and you are free to choose any of them. We will be mainly using typescript to ensure type safety in our code. I have not limited this project to being able to be deployed to a single cloud environment and this contains pretty much all the configuration files required to do so.

### The folder structure

```plaintext
|
|-- .husky
|     |-- pre-commit
|
|-- .vscode
|     |-- settings.json
|     |-- extensions.json
|
|-- @types
|     |-- express
|     |     |-- index.d.ts
|     |
|     |-- xss
|     |     |-- index.d.ts
|
|-- modules
|     |-- auth
|     |     |-- controllers.ts
|     |     |-- generateKeyPair.js
|     |     |-- helpers.ts
|     |     |-- index.ts
|     |     |-- routes.ts
|     |     |-- user.model.ts
|     |     |-- validators.ts
|     |     |
|     ..    ..
|
|-- uploads
|     |-- .gitkeep
|
|-- utils
|     |-- appConfig.ts
|     |-- helpers.ts
|     |-- rateLimiter.ts
|     |-- initRouter.ts
|     |-- initValidator.ts
|
|-- .dockerignore
|-- .env.sample
|-- .env
|-- .eslintignore
|-- .eslintrc.json
|-- .gitignore
|-- .prettierignore
|-- .prettierrc.json
|-- .slugignore
|-- Dockerfile
|-- Procfile
|-- docker-compose.yml
|-- index.ts
|-- nginx.conf
|-- package.json
|-- readme.md
|-- rebuild.sh
|-- tsconfig.json
|-- yarn.lock
```

### Initial Setups

Package.json is the first place you should look for when watching a new project. This contains useful configurations for the project like name, version, author, scripts, dependencies etc.

Here, I have tried to make this file as readable and expressive as possible. I have tried separating the scripts to the most atomic possible. This includes different scripts depending on different node environments like development, production and testing. I have kept this minimal and the dependencies section does not include anything so that the template can be used for pretty much any project.

```json
// package.json
{
  "name": "Tutorial App",
  "version": "1.0.0",
  "main": "build/index.js",
  "author": "MD Rashid Hussain <m3rashid.hussain@gmail.com>",
  "license": "MIT",
  "scripts": {
    "ci": "yarn install --production",
    "start": "NODE_ENV=development ts-node index.ts",
    "start:dev": "NODE_ENV=development ts-node-dev --respawn index.ts",
    "prod": "NODE_ENV=production node ./build/index.js",
    "prod:dev": "NODE_ENV=development node ./build/index.js",
    "keypair:dev": "node ./modules/auth/generateKeyPair.js",
    "keypair:setup": "cp -r ./uploads ./build/uploads && mkdir ./build/modules/auth/keys",
    "keypair:generate": "node ./build/modules/auth/generateKeyPair.js",
    "remove:build": "rm -r ./build || true",
    "build": "yarn remove:build && tsc && yarn keypair:setup && yarn keypair:generate",
    "format:check": "prettier --check .",
    "format:write": "prettier --write .",
    "lint:check": "eslint .",
    "lint:fix": "eslint --fix .",
    "precommit": "lint-staged",
    "prepare": "husky install"
  },
  "dependencies": {},
  "devDependencies": {
    "@types/node": "^18.0.0",
    "@typescript-eslint/eslint-plugin": "^5.28.0",
    "@typescript-eslint/parser": "^5.28.0",
    "eslint": "^8.18.0",
    "eslint-config-google": "^0.14.0",
    "eslint-config-prettier": "^8.5.0",
    "husky": "^8.0.1",
    "lint-staged": "^13.0.2",
    "prettier": "^2.7.1",
    "ts-node": "^10.8.1",
    "ts-node-dev": "^2.0.0",
    "typescript": "^4.7.4"
  },
  "lint-staged": {
    "*.{js,ts,tsx}": [
      "eslint",
      "prettier --write"
    ],
    "*.json": [
      "prettier --write"
    ]
  }
}
```

Clint helps to add enforce code quality and style of your code. This file contains useful configurations for these use cases.

```json
// .eslintrc.json
{
  "root": true,
  "env": {
    "es2021": true,
    "node": true,
    "browser": true
  },
  "extends": ["google", "prettier"],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaVersion": "latest",
    "sourceType": "module"
  },
  "plugins": ["@typescript-eslint"],
  "rules": {
    "no-console": "off",
    "require-jsdoc": 0,
    "new-cap": 0,
    "no-unused-vars": 0
  }
}
```

Prettier is used to format your code and keep it consistent across different developers. There can be cases where a certain developer in the team prefers double quotes for strings while others prefer single quotes, here prettier comes to the rescue. This ensures a consistent code formatting standard for the entire codebase and reformats to the defined configuration.

```json
// .prettierrc.json
{
  "trailingComma": "es5",
  "tabWidth": 2,
  "semi": false,
  "singleQuote": true
  ...
}
```

This file is useful when deploying to heroku (for testing purposes). Heroku (on free dynos) is not suitable for production deployments, as the dynos keep on going to sleep mode after a short interval of time of around 30 mins or so.

```python
# Procfile
web: yarn prod
```

Ignore the required files

*   \`\`\`.gitignore\`\`\` to ignore files from git
    
*   \`\`\`.eslintignore\`\`\` to ignore files from eslint checks
    
*   \`\`\`.prettierignore\`\`\` to ignore files from prettier checks
    
*   \`\`\`.dockerignore\`\`\` to ignore files from docker builds
    
*   \`\`\`.slugignore\`\`\` to ignore files from Heroku builds
    

Use .env to store all the actual dependencies and .env.sample to give a glimpse of what all environment variables are required in the project. For example

```python
# .env
AUTH_SECRET=thisisasupersecretauthsecretdonotdisclose

# .env.sample
AUTH_SECRET=
```

The \`\``tsconfig.json`\`\` file is responsible for defining standards and configurations to be used while compiling your typescript file to javascript to be able to run. These are my preferred configuration for tsconfig.json. This is not complete and there is a scope for improvement for a better developer experience

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "es2016", 
    "lib": [ "ES6" ],
    "module": "commonjs",
    "typeRoots": [ "./@types" ], 
    "allowJs": true,
    "checkJs": true,
    "outDir": "./build",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true, 
    "skipLibCheck": true,
    "baseUrl": "./",
  },
  "exclude": ["node_modules", "client"]
}

// the baseUrl: "./" can make our imports relative to the root directory and save from ../../../ syntax, we can use absolute-like imports. for example, import { User } from "modules/auth/user.model.ts" rather than "../../modules/auth/user.model.ts" 

// exclude the node_modules and client folders to opt out of type checkings in those folders, to speed up incremental builds
```

```bash
# .husky/pre-commit
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

yarn precommit
```

After adding this file, you need to make this file executable. In Linux, you can do something like (from the root of the project) `sudo chmod +x ./.husky/pre-commit`  
requires root user permissions

After this step, on installing any npm package, there would be an additional message saying "Git Hooks Installed". This ensures, your git hooks are properly set up. These git hooks can be later used to enforce styles, and run scripts (tests) before committing code to git.

### Setting up the boilerplate

```typescript
// index.ts
import { config } from 'dotenv'
config()
import cors from 'cors'
import helmet from 'helmet'
import xss from 'xss-clean'
import mongoose from 'mongoose'

import express, { NextFunction, Request, Response } from 'express'
import { appConfig, isProduction, startLog } from './utils/appConfig'
import { auth } from './modules'

const app = express() // inititializing an app instance
app.use(helmet())  // helmet to get rid of some bad headers 
app.use(xss())  // to prevent xss attacks
app.use(cors(appConfig.cors))  // for cross origin resourse sharing, as this is just an API server and the client is probabbly completely secluded from the server
app.use(express.json())  // to accept/send json as request/response 
app.use(express.urlencoded({ extended: true }))  // passing the request body to req.body object and parsing it as json
mongoose.set('debug', !isProduction)  // to see which queries are run on the database when in development environment

app.use(auth.authRouter)  // adding the router as a middleware
// other routes

// to get server status
app.all('/', (_: Request, res: Response) => {
  return res.json({ message: 'Server is OK' })
})

// Global error handler
// here, we can run scripts to get mail/sms to get notified
app.use((err: any, req: Request, res: Response, _: NextFunction) => {
  console.error(err)
  return res.status(500).json({
    message: appConfig.errorMessage,
  })
})

// to gracefully stop the server in case of any failure/exception 
// here, we can run scripts to get mail/sms to get notified
process.on('uncaughtException', (error: Error) => {
  console.error(error)
  process.exit(1)
})

// running the server
const port = process.env.PORT || 5000
app.listen(port, async () => {
  try {
    await mongoose.connect(appConfig.mongodbUri)
    console.log('Mongoose is connected')
    console.log(startLog(port))
  } catch (err) {
    console.error('MongoDB connection error')
    console.error(JSON.stringify(err))
    process.exit(1)
  }
})
```

This file contains all the configuration options for the website. Right now, it contains only some of the things, but this file is usually large and contains a lot of configurations

```typescript
// utils/appConfig.ts
import { CorsOptions } from 'cors'

export interface IAppConfig {
  cors: CorsOptions
  errorMessage: string | ((err: any) => string)
  mongodbUri: string
}

const devConfig: IAppConfig = {
  cors: {
    credentials: true,
    origin: ['http://localhost:3000', 'http://localhost:3001'],
    optionsSuccessStatus: 200,
  },
  errorMessage: (err: any) =>
    JSON.stringify(err.message) || 'Internal Server Error',
  mongodbUri: '...',
}

const prodConfig: IAppConfig = {
  cors: {
    credentials: true,
    origin: [
      // list of origin urls
    ],
    optionsSuccessStatus: 200,
  },
  errorMessage: 'Internal Server Error',
  mongodbUri: `...`,
}

export const isProduction = process.env.NODE_ENV === 'production'
export const startLog = (port: number | string) =>
  `Ready on port:${port}, env:${process.env.NODE_ENV}`

export const appConfig = isProduction ? prodConfig : devConfig
```

Rate-limiting users is a very important feature of the server. This prevents users from the same IP from spamming the server with multiple requests. This can prevent DOS and malicious hackers from spamming the server with pre-scripted requests.

```typescript
// utils/rateLimiters.ts
import rateLimit, { Options } from 'express-rate-limit'

const authRateLimitConfig: Partial<Options> = {
  windowMs: 5 * 60 * 1000, // 5 minutes
  max: 20, // Limit each IP to 20 requests per `window`
  standardHeaders: true,
}

const regularRateLimitConfig: Partial<Options> = {
  windowMs: 5 * 60 * 1000, // 5 minutes
  max: 100, // Limit each IP to 100 requests per `window`
  standardHeaders: true, // Return rate limit info in the `RateLimit-*` headers
}

export const authRateLimiter = rateLimit(authRateLimitConfig)
export const regularRateLimiter = rateLimit(regularRateLimitConfig)
```

This contains scripts to be used for all routers in the codebase. Here, I have put a make-safe function, which gets wrapped on the routes to automatically catch the errors, so there is no need to use try/catch for any controllers, you can throw errors directly inside the controllers without thinking about ways to catch the error. This reduces the code size and makes it more readable and maintainable.

```typescript
// utils/initRouter.ts
import { Request, Response, NextFunction } from 'express'

import { verifyJWT } from 'modules/auth/helpers'

// Global error checker
export const makeSafe =
  (check: Function) => (req: Request, res: Response, next: NextFunction) => {
    Promise.resolve(check(req, res, next)).catch(next)
  }

export const checkAuth = (req: Request, res: Response, next: NextFunction) => {
  const token = req.headers['authorization']
  if (!token) {
    return res.status(401).json('Unauthorized')
  }
  const { expired, payload } = verifyJWT(token)
  if (expired) {
    return res.status(401).json('Unauthorized')
  }
  // @ts-ignore
  req.userId = payload?.userId
  next()
}
```

```typescript
// utils/initValidator.ts
import { NextFunction, Request, Response } from 'express'
import Joi from 'joi'

export const initValidator = (schema: Joi.ObjectSchema) =>
  async (req: Request, res: Response, next: NextFunction) => {
    await schema.validateAsync({ ...req.body })
    next()
  }
```

### Creating the auth module

Authentication is one of the primary requirements of any API server and is typically found on almost all websites nowadays. This module is responsible for all the authentication logic (in-premise). This includes login, registering, forgetting/resetting passwords, deleting/recovering accounts, etc.

This module can go anywhere from level 0 to level 100 and can involve a lot of complexities depending on the use case and business requirements.

```typescript
// modules/auth/controllers.ts
import { Request, Response } from 'express'

export const getUser = async (req: Request, res: Response) => {
  // ... getting user from the userId got from jwt ...
}

// add your logic for these controllers
export const login = async (req: Request, res: Response) => {}
export const register = async (req: Request, res: Response) => {}
export const forgotPassword = async (req: Request, res: Response) => {}
export const resetPassword = async (req: Request, res: Response) => {}
export const deleteUser = async (req: Request, res: Response) => {}
export const recoverDeletedUser = async (req: Request, res: Response) => {}
```

```javascript
// modules/auth/generatekeyPair.js
const fs = require('fs')
const crypto = require('crypto')

const genKeyPair = () => {
  const keyPair = crypto.generateKeyPairSync('rsa', {
    modulusLength: 4096,
    publicKeyEncoding: { type: 'pkcs1', format: 'pem' },
    privateKeyEncoding: { type: 'pkcs1', format: 'pem' },
  })
  fs.writeFileSync(__dirname + '/keys/public.pem', keyPair.publicKey)
  fs.writeFileSync(__dirname + '/keys/private.pem', keyPair.privateKey)
}

genKeyPair()
```

```typescript
// modules/auth/helpers.ts
import fs from 'fs'
import path from 'path'
import JWT from 'jsonwebtoken'
import mongoose from 'mongoose'

const privateKey = fs.readFileSync(
  path.join(__dirname, './keys/private.pem'),
  'utf8'
)
const publicKey = fs.readFileSync(
  path.join(__dirname, './keys/public.pem'),
  'utf8'
)

export const issueJWT = (userId: mongoose.Types.ObjectId) => {
  const signedToken = JWT.sign({ userId }, privateKey, {
    algorithm: 'RS256',
    expiresIn: '1d',
  })
  return 'Bearer ' + signedToken
}

export const verifyJWT = (token: string) => {
  try {
    const extractedToken = token.split(' ')[1]
    const payload = JWT.verify(extractedToken, publicKey)
    return { expired: false, payload }
  } catch (err: any) {
    console.error({ 'Verify JWT error': err })
    return {
      expired: err.message.includes('jwt expired'),
      payload: null,
    }
  }
}
```

```typescript
// modules/auth/routes.ts
import { Router } from 'express'

import { checkAuth, makeSafe } from 'utils/initRouter'
import { authRateLimiter, regularRateLimiter } from 'utils/rateLimiters'
import {
  deleteUser,
  forgotPassword,
  getUser,
  login,
  recoverDeletedUser,
  register,
  resetPassword,
} from 'modules/auth/controllers'

const r = Router()

r.post('/auth/login', authRateLimiter, makeSafe(login))
r.post('/auth/register', authRateLimiter, makeSafe(register))
r.post('/auth', checkAuth, regularRateLimiter, makeSafe(getUser))
r.post('/auth/forgot-password', authRateLimiter /* other middlewares */)
r.post('/auth/reset-password', authRateLimiter /* other middlewares */)

r.post('/auth/forgot-password', authRateLimiter, makeSafe(forgotPassword))
r.post('/auth/reset-password', authRateLimiter, makeSafe(resetPassword))
r.post('/auth/delete-user', authRateLimiter, makeSafe(deleteUser))
r.post('/auth/recover-user', authRateLimiter, makeSafe(recoverDeletedUser))

export const authRouter = r
```

```typescript
// modules/auth/user.model.ts
import mongoose from 'mongoose'

export interface IUser {
  email: string
  password: string
  // other fields
}

const userSchema = new mongoose.Schema<IUser>({
    email: { type: String, required: true, unique: true },
    password: { type: String, required: true },
    // ... other fields ...
  }, { timestamps: true }
)

export const User = mongoose.model<IUser>('User', userSchema)
```

```typescript
// modules/auth/validator.ts
import Joi from 'joi'

import { initValidator } from 'utils/initValidator'

const login = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string().required(),
  // other fields you expect to have for login route
})

// ... other validators for register and other routes

export const loginValidator = initValidator(login)
// export other validators as this
```

```typescript
// modules/auth/index.ts
export * from 'modules/auth/user.model'
export * from 'modules/auth/routes'
```

So, this was it. This is how I prefer things to be done while creating a nodejs API server. Again, this may not be the best approach, but a good approach.

Share your insights on how you do this.