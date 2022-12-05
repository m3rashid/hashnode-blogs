# Scaling up Nodejs APIs (What I learnt so far)

When working on a project, we often get a few real nuggets here and there on how to do something in a better way. We get to learn retrospectively, and then we're fully prepared to apply it next time around.

This is my attempt to document some of the best Node.js scalability practices that I have learnt in my journey of learning with Nodejs.

#### With that said, here's what we will cover in this article:  

*   Use throttling
    
*   Optimize your database queries
    
*   Fail fast with circuit breaker
    
*   Log your checkpoints
    
*   Use Kafka over HTTP requests
    
*   Look out for memory leaks
    
*   Use caching
    
*   Use connection pooling
    
*   Seamless scale-ups
    
*   OpenAPI compliant documentation
    

#### Use throttling / Rate Limit

Rate Limiting your APIs is a must to have in any production-ready node API, or any server in general. There must be at least zero-level of security against [denial-of-service attacks](https://en.wikipedia.org/wiki/Denial-of-service_attack) over your API.

Throttling is a special term often used in implementing search bars where users send HTTP requests on every key press. Throttling allows you to limit access to your services to prevent them from being overwhelmed by too many requests. It has some clear benefits – you can safeguard your application whether it's a large burst of users or a denial-of-service attack. The common place to implement a throttling mechanism is where the rate of input and output don't match. Particularly, when there is more inbound traffic than what a service can (or wants to) handle.

On each request, you perform some computation and maybe database queries which can overload your server unnecessarily. So, you might come up with an idea of why not queue all those requests rather than reject them. There are some good reasons why you would not want to do it.

*   This creates a single point of failure for your entire app.
    
*   Depending on your architecture and business requirements, you would not want to allow your users to get more data over their subscription plan.
    
*   In most of the use cases, the extra traffic is useless (if you have set up your rate limit reasonably, depending on the use case and API hits).
    
*   Also, it doesn't have too many drastic effects on the user experience of your application.
    

#### Optimize your database queries

*   Choosing your database carefully. You can encounter some serious problems if you don't put on a lot of thoughts into your database. The choice of database solely depends on your use case. There are certain key points to keep in mind like "How ACID compliance do you want ?", "How much do you value data integrity and data security ?", and one of the biggest questions "Is the application a part of an existing infrastructure ?"
    
*   Use indexing for your tables, but do not overuse them. Indexing in a database comes at a cost.
    
*   Read only the fields you want in the result.
    
*   Decouple your database operations from your actual business logic. This will help in case you change databases or change the ORM (if you are using one) behind it.
    
*   Directly update the fields in the query, rather than fetching the document, updating it and then saving it back.
    

#### Log your checkpoints

*   A good logging setup allows you to spot errors quickly. You can create visualizations to understand your app's behaviour, set up alerts, and debug efficiently.
    
*   I would also suggest making a custom (global) error handler to handle the API programmatical/ logical/ network errors globally, rather than checking for the same errors in multiple places in your code
    
*   Good things to log include
    
*   Requests, as they enter the main control flow in your application and after they are validated and sanitized.
    
*   Request and response when interacting with an external service/SDK/API.
    
*   The final response to that request.
    
*   Helpful error messages for your catch handlers (with sane defaults for error messages).
    
*   If a request goes through multiple services during the lifecycle, you can pass along a unique ID in the logs to capture a particular request across all the services.
    

#### Look out for memory leaks

There are certain things recommended to check for memory leaks in your code while you are in the development phase.

*   Run your Node app with the `--inspect` flag.
    
*   Use the node debugger built into chrome using the `chrome://inspect/#devices`
    
*   Click `inspect` &gt; `Memory tab` &gt; `Allocation instrumentation` on timeline.
    
*   Perform some operations on your app. You can use apache bench on macOS to fire off multiple requests. Run curl [cheat.sh/ab](http://cheat.sh/ab) in your terminal to learn how to use it.
    
*   Stop the recording and analyze the memory retainers.
    
*   If you find any large blocks of retained memory, try to minimize it. There are a lot of resources on this topic.
    
*   Start by googling "how to prevent memory leaks in Node.js".
    
*   Profiling your Node.js application and looking for memory utilization patterns should be regular practice
    

#### Use caching

The goal is to not hit the database for every request your application gets. Storing the results in a cache decreases the load on your database and boosts performance.

There are two strategies when working with caching.

*   Write-through caching makes sure the data is inserted into the database and the cache when a write operation happens. This keeps the cache relevant and leads to better performance.
    
*   Lazy loading, the data is only written to the cache when it is first to read. The first request serves the data from the database but the consequent requests use the cache. It has a smaller cost but an increased response time for the first request.
    
*   Deciding the TTL (time to live) for your caching layer.
    
*   Caching is hard and finding the appropriate TTL for your API endpoint is challenging. There are times when you implement almost everything right, and you still get `Cache-Miss` where you expect not to, while you get `Cache-Hit` where you want fresh data.
    
*   One thing to keep in mind is, caching is a challenging task and whatever you do, you can't get it 100% right, until and unless you know what you are doing with it.
    

#### Use connection pooling

*   Opening a standalone connection to the database is costly. It involves TCP handshake, SSL, authentication and authorization checks, and so on. Instead, you can leverage connection pooling.
    
*   A connection pool holds multiple connections at any given time. Whenever you need it, the pool manager assigns any available/idle connection. You get to skip the cold start phase of a brand new connection.
    
*   But this comes at a cost. If you are using a lot of concurrent connections, it is putting a lot of load on the server hardware and you may run out of ample resources for your main process to run.
    
*   The more connections, the less RAM each connection has, and the slower the queries that leverage RAM (for example sort). The same principle applies to your disk and CPU. With every new connection, you are spreading your resources thin across the connections.
    

#### Seamless scale-ups

*   When your application's user base is starting to grow, you want to improve your server hardware or add more servers in parallel to cater to this new increasing demand. These are the two paradigms in scaling up.
    
    *   **Vertical Scaling** - This involves adding more resources to the same server like using more RAM, CPU and memory resources. In short, you opt for a higher-end machine to run your server.
        
    *   **Horizontal scaling** - This involves adding more machines to contribute to the same server parallelly. In short, if you were using a single machine earlier, now you would be using multiple machines for the same task. This comes with some additional complexity of now managing their health, resources, VPC, etc. Also, your codebase needs to be sufficiently stateless so that there is no inconsistency to the end user in terms of data or auth flow.
        
*   If you’re using AWS, you can leverage Automatic Scaling Groups (ASG) which horizontally scales the number of servers based on a predefined rule (for example when CPU utilization is more than 50%).
    
*   You can even pre-schedule the scale-up and scale-down using scheduled actions in case of predictable traffic patterns (for example during the World Cup finals for a streaming service).
    
*   Once you have your ASG in place, adding a load balancer in front will make sure the traffic is routed to all the instances based on a chosen strategy (like round robin, for example).
    
*   It is always a good idea to estimate the requests your single server can handle (CPU, memory, disk, and so on) and allocate at least 30% more.
    

These are some of the things I learnt about building API servers in nodejs or any other language for that matter. Comment down your thoughts