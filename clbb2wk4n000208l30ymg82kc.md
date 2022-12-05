# The Clean Architecture

#### by Robert C. Martin (Uncle Bob)

## Explanation

#### Philosophies over the architecture Resilient in the face of changes

*   Maintainable with increasing complexity
    
*   Independent of Frameworks - No direct dependency of the external package to the business logic. The architecture does not depend on the existence of some library of feature-laden software. This allows you to use such frameworks as tools, rather than having to cram your system into their limited constraints.
    
*   Testable - The business rules can be tested without the UI, Database, Web Server, or any other external element.
    
*   Independent of UI - The UI can change easily, without changing the rest of the system. A Web UI could be replaced with a console UI, for example, without changing the business rules.
    
*   Independent of Database - You can swap out Oracle or SQL Server, for Mongo, BigTable, CouchDB, or something else. Your business rules are not bound to the database.
    
*   Independent of any external agency - In fact, your business rules simply don’t know anything at all about the outside world.
    

#### Entities

*   In the centre, we have entities, the primary concepts of your business.
    
*   Let's say, you are building an application for an insurance company you might have an entity like a claim or you're building an entity for some company that sells products. You'll have entities like customer, order, and product. These are all that we call entities.
    
*   If you're building a social media application you might have an entity like a post, for example, or a comment.
    

#### Use Cases

*   Use cases are interactions between entities.
    
*   So if we're in our insurance company example you can say that you'd have a use case like filing a claim. Or for our retailer example, you might have a use case like a customer places an order.
    
*   Likewise in a social media setting, you might have user posts a tweet.
    

#### Adapters

*   Adapters are there to isolate our various use cases from the tools that we use so from the frameworks and drivers that we may be used to deliver our application and that's the final circle.
    

#### Frameworks and Drivers

*   The final circle is all about these frameworks and drivers that we all use to build our applications.
    

#### Data Flow and Dependency Injection

*   The next thing you'll notice is these arrows are pointing from the outermost circle down into the innermost circle and they go only in one direction. So those arrows signify the flow of dependency. They say that an outer circle can depend on an inner circle but an inner circle cannot depend on an outer circle, and that rule is the magic that makes this whole architecture hang together and work and deliver all kinds of value.
    
*   So if you think about it if the flow of dependencies goes from the outside in that means changes are gonna ripple from the inside out right. So if something changes in a framework or a driver since nothing is depending on that framework or driver, the change is isolated to that one circle. On the other hand if something changes in an entity well the use cases depend on our entities and the adapters depend on our use cases and the frameworks depend on our adapters. The change has the potential of affecting every layer and so what we do is we put the things that are most likely to change over time on the outside and the things that are less likely to change over time on the inside. And that's what makes this architecture so resilient in the face of change.
    
*   Of course, you might have noticed there's a problem with all this. If you've got a use case like place order for example customer places an order, how is that going to work without the use case being able to access the database?
    
*   Because chances are, the order is gonna have to be saved into a database and so you would think that the use case would have to call our adapter and talk to our database which would invert the flow of dependency. The little sub-diagram in the bottom right is all about this issue. We have got a controller and a presenter both of these are examples of adapters. And on the right-hand side, we've got our use case has been split into three boxes. In the centre, we have the use case interactor and that's the logic that governs the use case. Then on the bottom, we've got an input port and on the top an output port. You'll notice there's a tiny upper case "I" in the corner and so that tiny upper case I stands for an interface which means that the use case input port is an interface that is satisfied by the controller, and the use case output port is going to be an interface that will be implemented or satisfied by the presenter. All you need to be able to do is inject your dependencies rather than import them.
    

#### Conclusion

*   Conforming to these simple rules is not hard, and will save you a lot of headaches going forward. By separating the software into layers, and conforming to The Dependency Rule, you will create an intrinsically testable system, with all the benefits that imply. When any of the external parts of the system become obsolete, like the database, or the web framework, you can replace those obsolete elements with a minimum of fuss.
    

For more references, check out [this article by Robert C. Martin (Uncle Bob)](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)