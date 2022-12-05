# OOPS in Javascript/Typescript

Object-oriented programming is about creating objects that contain both data and methods.

Object-oriented programming has several advantages over procedural programming:

*   OOP is faster and easier to execute
    
*   OOP provides a clear structure for the programs
    
*   OOP helps to keep the Java code DRY "Don't Repeat Yourself", and makes the code easier to maintain, modify and debug.
    
*   OOP makes it possible to create fully reusable applications with less code and shorter development time
    

There are certain features or mechanisms which make a Language Object-Oriented like:

*   Classes
    
*   Object
    
*   Encapsulation
    
*   Inheritance
    

#### Classes Objects and Methods

Classes and objects are entities or building blocks of the application. For example, in real life, a car is an object. The car has attributes, such as weight and colour, and methods, such as drive and brake.

Objects are instances of a class and methods are the actions associated with the class. Like, A human can write, so writing is an action for humans and hence writing is a method for the objects of the Human class. The characteristics of an Object are called Properties in Object-Oriented Programming and the actions are called methods. Objects are everywhere in JavaScript, almost every element is an Object whether it is a function, array, or string. A javascript method is a property of an object whose value is a function.

A Class is like an object constructor or a "bgetDetailslueprint" for creating objects. A class can have many Objects because the class is a template while Objects are instances of the class or the concrete implementation.

Before we move further into implementation, we should know unlike other Object Oriented languages there are no classes in JavaScript we have only Object. To be more precise, JavaScript is a prototype-based Object Oriented Language, which means it doesn’t have classes, rather it defines behaviours using a constructor function and then reuses it using the prototype.

JavaScript classes, introduced in ECMAScript 2015, are primarily syntactical sugar over JavaScript’s existing prototype-based inheritance. The class syntax is not introducing a new object-oriented inheritance model to JavaScript. JavaScript classes provide a much simpler and clearer syntax to create objects and deal with inheritance.

\- [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)

#### Lets see classes in action

```javascript
class Human {
  constructor(name, gender, dob) {
    this.name = name;
    this.gender = gender;
    this.dob = dob;
  }

  getDetails() {
    return `${this.name} is a ${this.gender}, born on ${this.dob}`;
  }
};

const p1 = new Human('Rahul', 'boy', '31-01-2001');
const p2 = new Human('Abdul', 'boy', '01-01-1970');

console.log(p1.getDetails());
console.log(p2.getDetails());
```

Here I created a class named Human which has a method called `getDetails()` which returns the details of that human object as a string

#### Encapsulation

The process of wrapping properties and functions within a single unit is known as encapsulation.

```javascript
class Human {
  constructor(name, gender, dob) {
    this.name = name;
    this.gender = gender;
    this.dob = dob;
  }

  getDetails() {
    return `${this.name} was born on ${this.dob}. ${this.add ? "Lives in " + this.add : ""}`;
  }

  addAddress(add) {
    this.add = add;
  }
};

const p1 = new Human('Raju', 'boy', '21-02-1999');
p1.addAddress('Jamia Nagar, New Delhi');
p1.getDetails();
```

In the above example, I created a human using the constructor and initialize its properties and methods. Sometimes encapsulation refers to the hiding of data or data Abstraction which means representing essential features hiding the background detail. Most of the OOP languages provide access modifiers to restrict the scope of a variable, but there are no such access modifiers in JavaScript there are certain ways by which we can restrict the scope of variables within the Class/Object.

#### Inheritance

It is a concept in which some properties and methods of an Object are being used by another Object. Unlike most of the OOP languages where classes inherit classes, JavaScript Objects inherit Objects i.e. certain features (property and methods) of one object can be reused by other Objects.

```javascript
class Male extends Human {
  constructor(name, dob) {
    super(name, 'boy', dob);
    this.name = name;
    this.dob = dob;
  }

  work() {
    if(!this.lifeSpan) this.lifeSpan = 60;
    this.lifeSpan -= 5;
  }

  overWork () {
    if(!this.lifeSpan) this.lifeSpan = 60;
    this.lifeSpan -= 10;
    this.enjoy = false;
  }
}
```

In the above example, we define a Human class with certain properties and methods and then we inherit the Human class in the Male class and use all the properties and methods of the Human class as well as define certain properties and methods for the Male class.

This was just an introductory article on how to get started with object-oriented programming in javascript or typescript. Comment down your thoughts below