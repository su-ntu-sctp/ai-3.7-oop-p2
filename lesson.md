## Object Oriented Programming

Object Oriented Programming (OOP) is a programming paradigm that is based on the concept of objects. It is a way of organizing code into objects that have state (attributes) and behavior (methods).

OOP is based on 4 principles:

- Encapsulation
- Inheritance
- Polymorphism
- Abstraction

## Part 1: Encapsulation

### What is Encapsulation?

As explained in the previous lesson, encapsulation is

- Bundling of behaviours and attributes on a single object
- Hiding fields and some methods from public access

By setting fields to private, we are hiding them from public access. We can then provide public methods to access and modify the fields.

Let's define a `Person` class.

```java
public class Person {

  // FIELDS
  private String name;
  private int birthYear;

  // CONSTRUCTORS
  public Person() {
  }

  public Person(String name, int birthYear) {
    this.name = name;
    this.birthYear = birthYear;
  }

  // INSTANCE METHODS
  public void greet() {
    System.out.println("👋 Hello, my name is " + this.name + " and I am a " + (2023 - this.birthYear) + " year old person.");
  }

  public void doWork() {
    System.out.println(this.name + " is working.");
  }

  // GETTERS AND SETTERS
  public String getName() {
    return this.name;
  }

  public void setName(String name) {
    this.name = name;
  }

  public int getBirthYear() {
    return this.birthYear;
  }

  public void setBirthYear(int birthYear) {
    this.birthYear = birthYear;
  }
}

```

In this way, we can protect the fields from accidental changes and misuse.

For example we could prevent setting a negative age for a person.

```java
public void setBirthYear(int birthYear) {
  if (birthYear < 1900 || birthYear > 2021) {
    System.out.println("Invalid birth year.");
    return;
  }
  this.birthYear = birthYear;
}
```

If the `birthYear` field is public, we cannot prevent the user from setting an invalid value.

Create `App.java` and test this

```java
Person person = new Person("Tony Stark", 1975);
person.setBirthYear(0);
person.greet();
person.doWork();
```

The users of our public methods do not need to know how the method works. Even if we were to make some internal changes, the user would not be affected.

For example, if we were to change the criteria for a valid birth year, we would only need to change the `setBirthYear` method. The user would not need to change their code.

```java
public void setBirthYear(int birthYear) {
  // if (birthYear < 1900 || birthYear > 2021) {
  if (birthYear < 1970 || birthYear > 2020) {
    System.out.println("Invalid birth year.");
    return;
  }
  this.birthYear = birthYear;
}
```

---

## Part 2: Inheritance

### What is Inheritance?

Inheritance is organizing code into a parent-child hierarchy so that the child can inherit the properties and methods of the parent.

Take for example, a `Person` class. It is the base class of a hierarchy of classes for other child classes such as `Student`, `Teacher`, `Employee`, `Janitor` etc.

### Creating a child class

Let's create a `Student` class that inherits from the `Person` class. To do that, we use the `extends` keyword.

By inheriting from the `Person` class, the `Student` class will inherit all the fields and methods of the `Person` class. The `Person` class is also known as the **parent** class or **super** class. The `Student` class is also known as the **child** class or **sub** class.

In addition, we want to make `Student` different by declaring fields and methods specific to it.

```java
public class Student extends Person {
  private int studentId;
  private String course;
  private int yearEnrolled;
  private ArrayList<Double> grades;

  public Student(int studentId, String course, int yearEnrolled) {
    this.studentId = studentId;
    this.course = course;
    this.yearEnrolled = yearEnrolled;
    this.grades = new ArrayList<Double>();
  }


  // add getters and setters

  // add methods specific to Student
  public void addGrade(double grade) {
    this.grades.add(grade);
  }

  public double getAverageGrade() {
    double sum = 0;
    for (double grade : this.grades) {
      sum += grade;
    }
    return sum / this.grades.size();
  }
}
```

Note that a class may only extend from one parent class as Java does not support multiple inheritance.

```java
// ❌ This is not allowed
public class Student extends Person, Human {
  // ...
}
```

Let's modify our `greet()` method to show the class calling the method.

```java
public void greet() {
  System.out.println("👋 Hello, my name is " + this.name + " and I am a " + (2023 - this.birthYear) + " year old " + this.getClass().getSimpleName().toLowerCase() + ".");
}
```

Now we can test our `Student` class.

```java
public class App {
  public static void main(String[] args) {
    // Instantiating a Student object
    Student student = new Student(1, "Computer Science", 2022);

    // Calling methods from the Person (Parent) class
    student.setName("Tony");
    student.setBirthYear(1995);
    student.greet();
    student.doWork();

    // Calling methods from the Student (Child) class
    System.out.println("I am studying " + student.getCourse());
    student.addGrade(80);
    student.addGrade(90);
    System.out.println("My average grade is " + student.getAverageGrade());
  }
}
```

The `Student` instance has access to all the `public` methods of the `Person` class.

Currently, we have to manually initialize the `name` and `age` fields using the setter methods because they are in the `Person` class. We can improve this by calling a constructor that accepts those values as well.

But how do we initialize these values since the `Student` class does not have these fields?

### `super` keyword

The `super` keyword is like `this` but it refers to the parent class. It can be used to call the parent class constructor. Recall that the parent class is also known as the **super** class, hence that is what calling `super()` does.

Currently we are not using `super` yet but the `Student` class has default values for the `name` and `age` fields. This is because Java automatically called `super()` for us implicitly.

```java
public Student(int studentId, String course, int yearEnrolled) {
  super(); // This calls Person()
  this.studentId = studentId;
  this.course = course;
  this.yearEnrolled = yearEnrolled;
  this.grades = new ArrayList<Double>();
}
```

`super()` calls the no-argument constructor of the parent class when there are no arguments passed in. If we want to call a different constructor, we can do so by passing in the arguments. In this case we want to call the `Person` constructor that accepts `name` and `birthYear`.

Our student constructor will also take in `name` and `birthYear` as arguments. The `name` and `birthYear` will be passed to the `Person` constructor using `super(name, birthYear)`.

```java
public Student(String name, int birthYear, int studentId, String course, int yearEnrolled) {
  super(name, birthYear); // This calls Person(String name, int birthYear)
  this.studentId = studentId;
  this.course = course;
  this.yearEnrolled = yearEnrolled;
  this.grades = new ArrayList<Double>();
}
```

Note that we cannot call `super()` and `this()` in the same constructor. This is because both `super()` or `this()` must be the first statement in the constructor.

```java
public Student(String name, int birthYear, int studentId, String course, int yearEnrolled) {
  super(name, age); // This calls Person(String name, int age)
  this(studentId, course, yearEnrolled); // ❌ This is not allowed
}
```

Back to our `main`, we can now pass in the `name` and `birthYear` to the `Student` constructor.

```java
Student student = new Student("Tony", 1995, 1, "Computer Science", 2022);

// Calling methods from the Person (Parent) class
// student.setName("Tony");
// student.setBirthYear(1995);
student.greet();
student.doWork();

// Calling methods from the Student (Child) class
System.out.println("I am studying " + student.getCourse());
student.addGrade(80);
student.addGrade(90);
System.out.println("My average grade is " + student.getAverageGrade());
```

### The `protected` access modifier

In an earlier lesson we had mentioned about the `protected` access modifier. The `protected` access modifier allows the field to be accessed by the child class.

Let's add another method to `Student`.

```java
public void attendClass() {
  System.out.println(this.name + " is attending class.");
}
```

Notice that we cannot access it directly in the `Student` class. This is because the `name` field is declared in the `Person` class. We can only access it using the `getName()` method.

```java
public void attendClass() {
  System.out.println(this.getName() + " is attending class.");
}
```

If we want to access the `name` field directly, we can use the `protected` access modifier. The `protected` access modifier allows the field to be accessed by the child class.

```java
protected String name;
```

### 👨‍💻 Activity: Inheritance

Create a `Teacher` class that inherits from the `Person` class.

The `Teacher` class should have the following fields:

```java
private int teacherId;
private String subject;
private double salary;
```

Example test code:

```java
Teacher teacher = new Teacher("John", 1975, 1, "Mathematics", 5000);
// Calling Parent class methods
teacher.greet();
teacher.doWork();
// Calling Child class methods
System.out.println("I teach " + teacher.getSubject());
System.out.println("My salary is " + teacher.getSalary());
```

## Part 3: Polymorphism

### What is Polymorphism?

Polymorphism means many forms i.e. the ability of a method to have different behaviours in different situations.

There are 2 types of polymorphism:

1. Static or compile-time polymorphism
2. Dynamic or run-time polymorphism

### Compile-time Polymorphism

Compile-time polymorphism is also known as **method overloading**. It occurs when there are multiple methods with the same name but different parameters.

It is called compile-time polymorphism because the compiler determines which method to call based on the number and type of arguments passed in.

```java
public class Calculator {
  public int add(int num1, int num2) {
    return num1 + num2;
  }

  public int add(int num1, int num2, int num3) {
    return num1 + num2 + num3;
  }

  public double add(double num1, double num2) {
    return num1 + num2;
  }
}
```

The `add()` method is overloaded 3 times. The compiler will determine which method to call based on the number and type of arguments passed in.

```java
Calculator calculator = new Calculator();
System.out.println(calculator.add(1, 2)); // 3
System.out.println(calculator.add(1, 2, 3)); // 6
System.out.println(calculator.add(1.5, 2.5)); // 4.0
```

Let's **overload** the `doWork()` method in `Student` class by accepting a `String` argument.

```java
public void doWork(String work) {
  System.out.println(this.name + " is doing " + work);
}
```

And run it in `main`.

```java
student.doWork("homework");
```

### Runtime Polymorphism

Runtime polymorphism is also known as **method overriding**. It occurs when a child class overrides a method of the parent class.

It is called runtime polymorphism because the JVM determines which method to call based on the object type.

Overriding is useful because it allows us to define a method in the child class that has the same name and signature as the method in the parent class. This allows us to call the same method on different types of objects and get different results.

Let's **override** the `doWork()` method in the `Student` class.

In the `Student` class file, right click, "Source Action, "Override/Implement Methods...".

```java
@Override
public void doWork() {
  System.out.println(this.name + " is studying.");
}
```

Now when we run the following code, the `doWork()` method in the `Student` class will be called instead of the `doWork()` method in the `Person` class.

```java
student.doWork();
```

#### The `@Override` annotation

The `@Override` annotation is used to indicate that a method is being overridden. It is not required but it is good practice to use it.

This is because without the annotation, if we accidentally misspell the method name or change the method signature, the compiler will not be able to detect it and we will not get any error messages.

```java
// @Override
public void doWerk() {
  System.out.println(this.name + " is studying.");
}
```

And then when we run the following code, the `doWork()` method in the `Person` class will be called.

```java
Person student = new Student("John", 2000, 12345, "Computer Science", 2020);
student.doWork(); // I am working.
```

#### Runtime Polymorphism with `super`

In some cases, we may want to call the parent class method from the child class, in addition to defining a new behavior in the child class.

We can call the parent class method from the child class using the `super` keyword.

```java
public class Student extends Person {
  @Override
  public void doWork() {
    super.doWork();
    System.out.println("I am studying.");
  }
}
```

### 👨‍💻 Activity: Polymorphism

Override the `doWork()` method in the `Teacher` class to say that the teacher is teaching.

Also, override the `greet()` method in the `Teacher` class to say what the teacher teaches, in addition to the original greeting.

Test code:

```java
Teacher teacher = new Teacher("John", 1975, 1, "Mathematics", 5000);
teacher.greet();
teacher.doWork();
```

Example output:

```bash
# teacher.doWork() output
John is teaching.
# teacher.greet() output
👋 Hello, my name is John and I am a 48 year old teacher.
Also, I teach Mathematics.
```

---

## Part 4: Abstraction

Abstraction is when we generalize a set of characteristics and behaviors into a class.

For example, animals, vehicles, products, etc. are all abstract concepts. They are not tangible objects that we can touch or see. Of course, we know examples of vehicles such as cars, vans, lorries, buses but these are all more concrete objects. But it would be hard to say, draw a vehicle, without knowing what type of vehicle we are talking about. So, we say that a vehicle is an abstract concept.

Abstraction helps us to think about things as groups and generalize their functionalities.

We can do abstraction in Java using:

- Abstract classes
- Interfaces
- Superclasses

### Abstract Classes

An abstract class is a class that is declared with the `abstract` keyword. It can have abstract and non-abstract methods.

An abstract method is a method that is declared without an implementation. It is declared with the `abstract` keyword. The implementation **must** be provided by the subclass.

An abstract class cannot be instantiated. It can only be used as a superclass for other classes.

In the case of our `Person` class, we can make it an abstract class because we do not intend to instantiate it. We only want to use it as a superclass for other subclasses.

We can also make the `doWork()` method `abstract` because we do not want to define the behavior of the `doWork()` method in the `Person` class. We want to define the behavior of the `doWork()` method in the child classes.

```java
public abstract class Person {
  // ...

  public abstract void doWork();
}
```

Once we declare `Person` as `abstract`, it can only be used as a superclass for other classes.

Now when we try to instantiate the `Person` class, we will get an error.

```java
Person person = new Person("Tony Stark", 2000, 12345);
```

### Interfaces

An **interface** is a similar concept to an abstract class. It is declared with the `interface` keyword. Unlike abstract classes though, an interface cannot have instance variables. From Java 8 onwards, it can also have default methods and static methods.

An interface can be named in different ways depending on what it represents:

1. **Capability/Behavior interfaces** - Often named with the suffix `able` to indicate what a class can do:

   - Examples: `Drivable`, `Runnable`, `Comparable`, `Trackable`

2. **Contract/Type interfaces** - Named as nouns to define what something is:

   - Examples: `List`, `Map`, `Set`, `Building`, `Vehicle`

3. **Service interfaces** - Named to represent services or operations:
   - Examples: `Repository`, `Service`, `Handler`, `Manager`

The key is that interface names should clearly communicate the contract or capability they define.

Interfaces allow us to specify what a class must do, without specifying how it should do it.

Using interfaces allows us to define a common behaviour that can be shared among multiple classes. This is useful when we want to define a common behavior for classes that are not related to each other.

For example, we might want to have a common behaviour, say `Trackable` for a `Car` as well as a `MobilePhone`. The `Car` and the `MobilePhone` are not related conceptually but yet we may need to implement a trackable behaviour for both. We can define a `Trackable` interface and have both the `Car` and `MobilePhone` classes implement the `Trackable` interface.

Create a file `LearnInterfaces.java` and code along. If you wish to do it in a single file, just omit the `public` keyword for the following interface and classes.

```java
public interface Trackable {
  // public abstract void track();
  // public and abstract are not required
  void track();
}
```

Any method declared in an interface is by default `public` and `abstract`. So, we do not need to specify the `public` and `abstract` keywords.

To use the `Trackable` interface, we need to implement it in the `Car` and `MobilePhone` classes with the `implements` keyword.

```java
public class Car implements Trackable {
  private String name;
  private int year;

  public Car() {
  }

  public Car(String name, int year) {
    this.name = name;
    this.year = year;
  }

  @Override
  public void track() {
    System.out.println("Tracking car " + this.name + " from " + this.year + ".");
  }

  public String getName() {
    return this.name;
  }

  public void setName(String name) {
    this.name = name;
  }
}
```

```java
public class MobilePhone implements Trackable {
  private String model;

  public MobilePhone() {
  }

  public MobilePhone(String model) {
    this.model = model;
  }

  @Override
  public void track() {
    System.out.println("Tracking mobile phone " + this.model + ".");
  }
}
```

And test it out with the following code.

```java
Car car = new Car("Toyota", 2022);
car.track();

MobilePhone phone = new MobilePhone("iPhone 14");
phone.track();
```

Unlike inheritance, a class can implement multiple interfaces. A car can be `Trackable` as well as `Drivable`.

Let's define a `Drivable` interface.

```java
public interface Drivable {
  void drive();
  void stop();

  // Java 8 onwards - default method
  default void honk() {
    System.out.println("Honk!");
  }
}
```

Default methods were added in Java 8. This was because previously it was not possible to add new methods to an interface without breaking the existing implementations of the interface.

Now, the classes that implement the interface can choose to override the default method with their own implementation, or they can simply use the default implementation.

```java
public class Car implements Trackable, Drivable {
  // ...
  @Override
  public void track() {
    System.out.println("Tracking car...");
  }

  @Override
  public void drive() {
    System.out.println("Driving car...");
  }

  @Override
  public void stop() {
    System.out.println("Stopping car...");
  }
}
```

Test the `Car` class with the following code.

```java
Car car = new Car("Toyota", 2022);
car.track();
car.drive();
car.stop();
car.honk();
```

### 👨‍💻 Activity: Abstraction

In this activity, you will practice working with abstract class and interfaces.

Create an `abstract` class `Vehicle`. There will be 2 child classes `Car` and `ElectricCar` that will extend the `Vehicle` class.

```java
public abstract class Vehicle {
  private String make;

  // Add your constructors, getters and setters
}
```

There are 4 interfaces that can be implemented: `Drivable`, `Trackable`, `FuelTank` and `BatteryPack`.

```java
interface Drivable {
  void drive();
  void stop();
}

interface Trackable {
  void track();
}

interface FuelTank {
  void fill();
  double getFuelLevel();
}

interface BatteryPack {
  void charge();
  double getCharge();
}
```

All vehicles are `Drivable` and `Trackable`. A `Car` should implement `FuelTank` and an `ElectricCar` should implement a `BatteryPack`.

To show that the interface can be used across unrelated classes, create a `MobilePhone` class that implements `Trackable` and `BatteryPack`. And an `RCCar` (remote controlled car) that implements `Drivable` and `BatteryPack`.

Test code:

```java
Car car = new Car("Toyota");
car.track();
car.drive();
car.stop();
car.fill();
car.getFuelLevel();

ElectricCar electricCar = new ElectricCar("Tesla");
electricCar.track();
electricCar.drive();
electricCar.stop();
electricCar.charge();
electricCar.getCharge();

MobilePhone phone = new MobilePhone();
phone.charge();
phone.getCharge();

RCCar rcCar = new RCCar();
rcCar.drive();
rcCar.stop();
rcCar.charge();
rcCar.getCharge();
```

### Interfaces vs Abstract Classes

You may have noticed that interfaces can be nouns, just like abstract classes. So, when should we use an interface and when should we use an abstract class?

```java
public interface Vehicle {
  void start();
  void stop();
}

public abstract class Vehicle {
  abstract void start();
  abstract void stop();
}
```

Use an interface when:

- You want to define a contract that can be implemented by any class, regardless of its position in the class hierarchy.
- You need to implement multiple behaviors in a class, as a class can implement multiple interfaces.

Use an abstract class when:

- You want to provide a common base implementation for a group of related classes.
- You need instance variables or methods that can be shared among subclasses.

## Part 5: Composition

Another concept of OOP is composition. Building a class out of simpler classes is called composition. It is a way to combine simple objects or data types into more complex ones.

To prefer **composition over inheritance** is an OOP principle that means that we should prefer to compose classes rather than inherit from them.

For more info: https://en.wikipedia.org/wiki/Composition_over_inheritance

Composition allows us to reuse code without having to inherit from a class. Inheritance is a tightly coupled relationship. If we inherit from a class, we are tightly coupled to that class. If the superclass changes, the subclass will also be affected.

Composition is a loosely coupled relationship. If we compose a class, we are not tightly coupled to that class. If the composed class changes, the class that uses it will not be affected.

Sometimes it is easier to model real-world objects using composition than trying to find commonalities between them and using inheritance.

For example, a car is composed of an engine, wheels, seats, etc. A car is not a type of engine or a type of wheel. A car has an engine and wheels.

Inheritance defines a **IS-A** relationship. Composition defines a **HAS-A** relationship. A car **IS-A** vehicle. A car **HAS-A** engine.

Let's say we have a `Radio` class now. One way for our `Car` and `ElectricCar` to have a radio is to put it in the `Vehicle` class. But not all vehicles have a radio.

Instead, we can create a `Radio` class and add it in the `Car` and `ElectricCar` classes.

We do not want to define the `Radio` in the `Vehicle` class as that would mean all child classes would have a `Radio`. We would also not want to define the Radio in the `Car` and `ElectricCar` classes as we would be repeating code.

```java
public class Radio {
  private String model;

  public Radio(String model) {
    this.model = model;
  }

  public String getModel() {
    return this.model;
  }

  public void setModel(String model) {
    this.model = model;
  }

  public void play() {
    System.out.println("Playing radio...");
  }

  public void stop() {
    System.out.println("Stopping radio...");
  }
}
```

```java
public class Car {
  private String make;
  private Radio radio;

  public Car(String make) {
    this.make = make;
    this.radio = new Radio("Sony");
  }

  public void drive() {
    System.out.println("Driving car...");
  }

  public void stop() {
    System.out.println("Stopping car...");
  }

  public void playRadio() {
    this.radio.play();
  }
}
```

```java
public class ElectricCar {
  private String make;
  private Radio radio;

  public ElectricCar(String make) {
    this.make = make;
    this.radio = new Radio("Sony");
  }

  public void drive() {
    System.out.println("Driving electric car...");
  }

  public void stop() {
    System.out.println("Stopping electric car...");
  }

  public void playRadio() {
    this.radio.play();
  }
}
```

---

END