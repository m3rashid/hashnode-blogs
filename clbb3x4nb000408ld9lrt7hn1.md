# MongoDB Sucks (or does it not ?)

MongoDB is an awesome solution, thanks to its document-oriented NoSQL features and capability of MapReduce calculation. The vision behind MongoDB was actually to keep the maximum number of functionalities possible and at the same time allow horizontal scale for making the developer’s life far more convenient and easier. It is positioned between the high-performance yet simple and uncomplicated key-value store and the powerful yet inefficient relational database system.

No doubt, I love MongoDB due to its flexibility and ease of use. If you don't know, how your app is going to be in the future and have no idea of what data models to design, you can go with MongoDB and enjoy the flexibility. Just dump the JSON (as bson to be more specific) in the database and don't care about that shit again.

MongoDB may seem to be a cool database but you must make sure that it is the perfect database for you before making the final choice. You need to examine the advantages and also disadvantages of the database. Nothing could be more disastrous than you building up your entire application on a specific database model but later on you learn that they just are not compatible with each other.

### Merits of MongoDB

*   Capable of Load Balancing & Sharding  
    Sharding is a type of database partitioning that separates large databases into smaller, faster, more easily managed parts. These smaller parts are called data shards. The word shard means "a small part of a whole." Read more about sharding [here](https://www.techtarget.com/searchoracle/definition/sharding). and [here](https://www.geeksforgeeks.org/what-is-sharding/)  
    If you have loads of data or just in case, you wish to distribute your database’s traffic among diverse machines for balancing the load, MongoDB is supposed to be much more advantageous than any other traditional database. Sharding is supposed to be MongoDB’s distinctive approach meant specifically for fulfilling the needs of data growth. It utilizes horizontal scaling and lets you use multiple machines to support data growth.
    
*   Flexible (change-friendly design)  
    MongoDB requires no data structures which seem to be unified across all objects that are in use. This feature makes MongoDB much easier and simpler to use than RDBMS. On the contrary, data consistency is really crucial at times and it is usually supposed to be a great thing, hence, it is recommended that you utilize a unified data structure.
    
*   Super-Speed  
    MongoDB is supposed to be extremely quick as all data is usually found at one single location. This holds well, only when the data being worked upon is in reality, a document. In case the data you are working on emulates a relational model one single document could be retrieved after carrying out several independent queries and this would certainly make it much slower when compared to an RDBMS.
    

### Use Cases where mongoDB can be a good option

*   Logging service  
    For loggers, you just need a place to dump all your logs and in this regard, MongoDB is quite a good database where you can store logs. In general, you can use it for any unrelated/secluded data
    
*   Loads of data involved
    
*   Analytics
    
*   Unstructured data  
    where your data does not have a well-defined structure, and you want to store it.
    

I have been using MongoDB for most of my dev journey and had a very welcoming response from it and I am sure most of us as javascript developers have similar experiences. But, when you go deep inside the inner workings, your data model/structure of your data, performance etc. things start to fall apart.

Below are some of the points, due to which I think MongoDB is not quite good for most of our needs.

*   More Memory  
    MongoDB is known to consume more memory as it is known to store every document along with the key name. Just because joins and slow queries are not possible you seem to end up quite often, dealing with duplicate data.
    
*   Not good at maintaining relationships (Absence of Joins)  
    Quite similar to a relational database, it is just not possible to have joins in MongoDB. If you ever require the actual functionality of joins, it is necessary to go about creating multiple queries which you would require to join manually very much in the code.
    
*   no support for transactions  
    certain atomic operations are supported, at a single document level
    
*   Certain operations result in a full database lock (ex. write operations) leading to concurrency issues
    
*   Inconsistent by design (lack of standardisation)
    
*   If all you need is a document database MongoDB serves the purpose very well
    

This point of not able to maintain relationships between your data and not able to perform joins on your data is quite a big issue. In today's times, I can't imagine a medium to large application, which does not include any relation. In any application that involves uses user authentication, you need user table and something is always related to the user, being it user centric in the app.

Let's imagine a scenario where an app involves users. Users can comment on posts and each comment can have multiple reactions from any other user. Using mongodb in such a scenario, you have two options

```javascript
// option 1 Involves subdocuments
import mongoose from "mongoose";

interface IComment {
  body: string;
  reactions: string[];
}

interface IUser {
  username: string;
  password: string;
  comments: IComment[];
}

const userSchema = new mongoose.Schema<IUser>(
  {
    username: { type: String, required: true },
    password: { type: String, required: true },
    comments: [
      {
        body: String,
        reactions: [{ type: String }],
      },
    ],
  },
  { timestamps: true }
);

export const User = mongoose.model<IUser>("User", userSchema);
```

```javascript
// option 2 involves imitating a RDBMS
import mongoose from "mongoose";

interface IReaction {
  reaction: string;
}

const reactionSchema = new mongoose.Schema<IReaction>(
  { reaction: String },
  { timestamps: true }
);

export const Relation = mongoose.model<IReaction>("Relation", relationSchema);

interface IComment {
  body: string;
}

const commentSchema = new mongoose.Schema<IComment>(
  {
    body: String,
    reactions: {
      type: mongoose.Schema.Types.ObjectId,
      rel: "Relation",
    },
  },
  { timestamps: true }
);

export const Comment = mongoose.model<IComment>("Comment", commentSchema);

interface IUser {
  username: string;
  password: string;
  comments: IComment[];
}

const userSchema = new mongoose.Schema(
  {
    username: { type: String, required: true },
    password: { type: String, required: true },
    comments: [
      {
        type: mongoose.Schema.Types.ObjectId,
        rel: "Comment",
      },
    ],
  },
  { timestamps: true }
);

export const User = mongoose.model<IUser>("User", userSchema);
```

The second approach looks legit and sensible but it is seems like a crazy hack to imitate a RDBMS design and this certainly comes with some caveats due to inherent nature of how mongoDB is designed.

One of the most important lackings of mongodb is the inability to perform transactions like in RDBMS. If you handle sensitive data in your application involving financial and medical data, you cant afford data loss. RDBMS handles this using something called ACID properties, which are as far as i know, not baked into mongoDB.

But, despite all this, mongoDB has seen rapid growth and improvements in the recent past and has incorporated a lot of improvements.