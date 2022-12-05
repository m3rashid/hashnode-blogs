# The package.json

If you've worked with Node.js before, you have likely encountered a package.json file. It is a JSON file that lives in the root directory of your project. Your package.json holds important information about the project. It contains human-readable metadata about the project (like the project name and description) as well as functional metadata like the package version number and a list of dependencies required by the application. Package.json is the first place you should look for while exploring any node-based codebase.

A sample package.json looks like as follows

```json
{
  "name": "demo-project",
  "version": "1.0.0",
  "description": "Description of demo project",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {},
  "devDependencies": {},
  "repository": {
    "type": "git",
    "url": "https://github.com/<username>/<repository>.git"
  },
  "author": "Author name",
  "contributors": [
    {
      "name": "Demo User",
      "email": "demo@user.com"
    }
  ],
  "keywords": ["project", "package"]
}
```

### Purpose

Your project's package.json is the central place to configure and describe how to interact with and run your application. It is used by the npm/yarn/pnpm CLI to identify your project and understand how to handle the project's dependencies. It's the package.json file that enables npm to start your project, run scripts, install dependencies, publish to the NPM registry, and many other useful tasks. The npm/yarn/pnpm CLI is also the best way to manage your package.json because it helps generate and update your package.json file throughout a project's life.

Your package.json fills several roles in the lifecycle of your project, some of which only apply to packages published to NPM. If you're not publishing your project to the NPM registry or otherwise making it publicly available to others, your package.json is still essential to the development flow.

Your project also must include a package.json before any packages can be installed from NPM. This is probably the top reason why you need one for your project.

  

## Important sections

### The LICENCE Section

This is a very important but often overlooked property. The license field lets us define what license applies to the code the package.json is describing. Having a clear license in place helps clearly define what terms the software is able to be used under.

The value of this field will usually be the license's identifier code -- a string like "MIT" or "ISC" for the MIT license and ISC license respectively. If you don't wish to provide a license, or explicitly do not want to grant use of a private or unpublished package, you can put "UNLICENSED" as the license. [Choose a License](https://choosealicense.com/) is a helpful resource if you're not sure which license to use. Learn more about licences at [Open Source Initiative](https://opensource.org/licenses/)

#### Popular Licences

*   [Apache License 2.0](https://opensource.org/licenses/Apache-2.0)
    
*   [BSD 3-Clause "New" or "Revised" license](https://opensource.org/licenses/BSD-3-Clause)
    
*   [BSD 2-Clause "Simplified" or "FreeBSD" license](https://opensource.org/licenses/BSD-2-Clause)
    
*   [GNU General Public License (GPL)](https://opensource.org/licenses/gpl-license)
    
*   [GNU Library or "Lesser" General Public License (LGPL)](https://opensource.org/licenses/lgpl-license)
    
*   [MIT license](https://opensource.org/licenses/MIT)
    
*   [Mozilla Public License 2.0](https://opensource.org/licenses/MPL-2.0)
    
*   [Common Development and Distribution License](https://opensource.org/licenses/CDDL-1.0)
    
*   [Eclipse Public License version 2.0](https://opensource.org/licenses/EPL-2.0)
    

### Scripts Section

The scripts field is another functional piece of metadata in your package.json. The scripts property takes an object with its keys being scripts we can run with npm run &lt;scriptName&gt;, and the value is the actual command which is run. These are typically terminal commands, which we put into the scripts field so we can both document them and reuse them easily.

Scripts are powerful tools that the npm CLI can use to run tasks for your project. They can do the job of most task runners used during development.

Using this we can simply run certain scripts at certain times during the lifecycle of the project. There is Some important advice to follow while defining your scripts. The first is to use the constructors (pre/script/post) forms. As the name suggests, it is to run certain scripts automatically after the defined script ran successfully. For example

```json
// sample scripts given
{
  // ...
  "scripts": {
    "prehello": "node before.some_script.js",
    "hello": "node some_script.js",
    "posthello": "node after.some_script.js"
  }
}
```

In, the above example, if you run with the command `npm run hello` or `yarn hello` or `pnpm run hello`, (depending on your project configuration), it will first run the `prehello` the script, then the `hello` the script, and then the `posthello` script.

This is helpful because, at certain times, before you run your script, you want your environment in a certain state for a variety of reasons. The same goes with the postscripts, you want your environment/node process to do some cleanup actions or anything after that script ran.

### Engines section

You can specify the version of the node that your stuff works on. This is extremely useful when deploying your project to a remote server or when you are in a collaborative project. This is an agreement that the code runs well on these system configurations. Apart from the given sample configurations, you can define a lot of other configuration options, refer to the npm docs for detailed documentation.

```json
{
  // ...
  "engines": {
    // you can give exact versions or .x to match
    "node": ">=14.x <18.x",
    // you can give configs,
    // if your project depends on npm or yarn
    "npm": "~1.0.20" // or "yarn": "....",
    // this os config says that the process can run on
    // linux or darwin machines.
    // but not on windows 32 bit arch machine.
    "os": [
      "linux", "darwin",  "!win32"
    ]
  }
}
```

Besides these, there are some commonly known fields, I have not discussed them here, because they are more or less self-explanatory. Some of them are

*   name (name of the project)
    
*   description (description of the project)
    
*   dependencies (other npm/... packages on which the code depends)
    
*   devDependencies (same as dependencies, but these are not needed in the production)
    
*   version (version of the project)
    
*   keywords (keywords for searching this project, useful when publishing your code as an npm package)
    
*   homepage (homepage of the page, if it is a website)
    
*   main (starting position (root) of the project)
    
*   bugs (where to report bugs, if found)
    
*   author (main author of the project)
    
*   contributors (other contributors of the project)
    
*   funding (who all funded this project)
    
*   repository (about the repository, if the codebase is hosted somewhere)
    

To learn more, you can read the official [NPM documentation](https://docs.npmjs.com/cli/v8/configuring-npm/package-json)