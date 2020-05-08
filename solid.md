# About SOLID

## Firstly

It's about OOP.

SOLID Principles strive to reduce the change of modules to their addition and removal.

## SRP: The Single Responsibility Principle

**A module should be responsible to one, and only one, actor.**

**A module should have one, and only one, reason to change.**

Example: We have a class for example Shape and we have a method that serialize Shape.

If we implement serialize in class Shape we break this principle. Because mechanizm of serialization can be useful for different classes for example class Person. And we should create a new class like Serializer that can apply different classes.

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

Example1: Suppose there is a base class Shape and its descendants `Circle` and Eectangle. And there is a certain function Foo (`List<Shape>` list). We believe that `List<Circle>` can be reduced to `List<Shape>`. However, it is not. Suppose, you can add any shape, for example, a rectangle. Initially, the list should contain only objects of the `Circle` class.

Example2:

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
