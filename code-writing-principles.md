# Сode writing principles

## DRY - Don't Repeat Yourself

### A basic strategy for reducing complexity to managable units is to divide a system into pieces

### The DRY principle states that these small pieces of knowledge may only occur exactly once in your entire system

### Every piece of knowledge must have a single, unambiguous, authoritative representation within a system

When you find yourself writing code that is similiar or equal to something you've written before, take a moment to think about what you're doing and don't repeat yourself.

## Principle - Keep it Simple Stupid

**Try to identify the following parts:**

* Functionality that has an inappropriate ratio between benefit and efforts.
* Functionality that is highly dependent on other functionality.
* Functionality that is likely to grow in complexity.

## Principle - You "Ain't Gonna Need It

You do not need to write features that will not be used in the future.

It's about additional work. You think this feature may be useful in the project, but it is not.

## SOLID

SOLID Principles strive to reduce the change of modules to their addition and removal.

## SRP: The Single Responsibility Principle

**A module should be responsible to one, and only one, actor.**

**A module should have one, and only one, reason to change.**

Example: We have a class for example `Shape` and we have a method that serialize `Shape`.

If we implement `serialize` in class `Shape` we break this principle. Because mechanizm of serialization can be useful for different classes for example class `Person`. And we should create a new class like `Serializer` that can apply different classes.

This logic we can apply for formatters. For example if we implement `toString` method for some class `Square` it should be better if we create class `ShapeFormatter` and implement logic in it. It will be better to support and extend this code.

## OCP: The Open Closed Principle

**A software artifact should be open for extension but closed for modification.**

**You should be able to extend a classes behavior, without modifying it.**

Break principle

```js
class Square {
    constructor(side) {
        this.side = side;
    }
}

class Rect {
    constructor(sideA, sideB) {
        this.sideA = sideA;
        this.sideB = sideB;
    }
}

class CalcSquare {
    getSum(shapes) {
        let sum = 0;

        for(let i = 0; i < shapes.length; i++) {
            const shape = shapes[i];
            if (shape instanceof Square) {
                sum += shape.side * shape.side;
            }
            if (shape instanceof Rect) {
                sum += shape.sideA * shape.sideB;
            }
        }
    }
}
```

Right example

```js
class Square {
    constructor(side) {
        this.side = side;
    }

    getSquare() {
        return this.side * this.side;
    }
}

class Rect {
    constructor(sideA, sideB) {
        this.sideA = sideA;
        this.sideB = sideB;
    }

    getSquare() {
        return this.sideA * this.sideB;
    }
}

class CalcSquare {
    getSum(shapes) {
        let sum = 0;

        for(let i = 0; i < shapes.length; i++) {
            sum += shapes[i].getSquare();
        }
    }
}
```

## LSP: The Liskov Substitution Principle

**Child classes should never break the parent class' type definitions.**

The concept of this principle was introduced by Barbara Liskov in a 1987 conference keynote and later published in a paper together with Jannette Wing in 1994.

**Subtypes must be substitutable for their base types.**

Break principle

```js
class Geometry {
    getBorderWidth() {}
}

class Square extends Geometry {
    constructor(borderWidth) {
        this.borderWidth = borderWidth;
    }
    getBorderWidth() {
        return this.borderWidth;
    }
}

class Point extends Geometry {}

const geometryArray = [new Square(10), new Point()];
let sum = 0;
for(let i = 0; i < geometryArray.length; i++) {
    // this throw error because Point without border
    sum += geometryArray[i].getBorderWidth();
}
```

Right version

```js
class Geometry { }

class Point extends Geometry {}

class Shape extends Geometry {
    getBorderWidth() {}
}

class Square extends Shape {
    constructor(borderWidth) {
        this.borderWidth = borderWidth;
    }
    getBorderWidth() {
        return this.borderWidth;
    }
}

const shapeArray = [new Square(10)];
let sum = 0;
for(let i = 0; i < shapeArray.length; i++) {
    sum += shapeArray[i].getBorderWidth();
}
```

## ISP: The Interface Segregation Principle

**Make fine grained interfaces that are client specific.**

**The interface-segregation principle (ISP) states that no client should be forced to depend on methods it does not use.**

Break rule

```js
class Animal {
    fly() { console.log('I can fly'); }
    run() { console.log('I can run'); }
}
class Dog extends Animal {
    fly() { return null }
}
class Cat extends Animal {
    fly() { return null }
}
class Eagle extends Animal {
    run() { return null }
}
new Dog().fly();
new Dog().run();
new Cat().fly();
new Cat().run();
new Eagle().fly();
new Eagle().run();
```

Right case

```js
class Animal {
}
class Dog extends Animal {
}
class Cat extends Animal {
}
class Eagle extends Animal {
}

const flyer = {
    fly: () => { console.log('I can fly'); }
}
const runner = {
    run() { console.log('I can run'); }
}

Object.assign(Dog.prototype, ...runner);
Object.assign(Cat.prototype, ...runner);
Object.assign(Eagle.prototype, ...flyer);

new Dog().run();
new Cat().run();
new Eagle().fly();
```

## DIP: The Dependency Inversion Principle

**Depend on abstractions, not on concretions.**

**Upper level modules should not depend on lower level modules. Both types of modules should depend on abstractions.**

**Abstractions should not depend on the details. Details should depend on abstractions.**

Break principle

```js
function getData(type, payload) {
    if (type === 'db') {
        const db = new Database();
        return db.get(payload);
    }
    if (type === 'localstorage') {
        return localstorage.get(payload);
    }
}
```

Right case

```js
class Client {
    constructor(type) {
        if (type === 'db') {
            this.source = new Database();
        }
        if (type === 'localstorage') {
            this.source = localstorage;
        }
    }
    get(payload) {
        return this.source.get(payload);
    }
}

const client = new Client('db');
client.get('notifications');
```
