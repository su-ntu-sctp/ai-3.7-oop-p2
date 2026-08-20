# Lesson: Object-Oriented Programming — Encapsulation, Inheritance, Polymorphism, Abstraction

## Lesson Overview

This lesson deepens OOP fundamentals by applying the four pillars, encapsulation, inheritance, polymorphism, and abstraction, plus composition. You will implement access control, constructors, `super`/`this`, `protected`, interfaces vs. abstract classes, method overloading/overriding, and design HAS-A vs. IS-A relationships with concise, practical examples.

The `Customer` class from the previous lesson is the starting point here. You will grow it into a small class hierarchy of customer types, which is the same shape of model you will later expose through a REST API.

## Lesson Objectives

- Apply inheritance using `extends`, `super`, and `protected` to build class hierarchies.
- Differentiate method overloading (compile-time) and overriding (runtime polymorphism).
- Design abstractions using abstract classes and interfaces.
- Model HAS-A relationships using composition.

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

Let's define a `Customer` class. This is the same idea as the class you built in the previous lesson, with validation added.

```java
public class Customer {

  // FIELDS
  private String name;
  private int joinYear;

  // CONSTRUCTORS
  public Customer() {
  }

  public Customer(String name, int joinYear) {
    this.name = name;
    if (validateJoinYear(joinYear)) {
      this.joinYear = joinYear;
    }
  }

  // INSTANCE METHODS
  public void displayProfile() {
    if (this.joinYear == 0) {
      System.out.println("Customer: " + this.name + " (join year not set).");
      return;
    }
    int currentYear = java.time.Year.now().getValue();
    System.out.println("Customer: " + this.name + ", " + (currentYear - this.joinYear)
        + " years with us. Type: " + this.getClass().getSimpleName() + ".");
  }

  public void calculateDiscount() {
    System.out.println(this.name + " gets the standard discount.");
  }

  // VALIDATION
  private boolean validateJoinYear(int joinYear) {
    if (joinYear < 1990 || joinYear > java.time.Year.now().getValue()) {
      System.out.println("Invalid join year.");
      return false;
    }
    return true;
  }

  // GETTERS AND SETTERS
  public String getName() {
    return this.name;
  }

  public void setName(String name) {
    this.name = name;
  }

  public int getJoinYear() {
    return this.joinYear;
  }

  public void setJoinYear(int joinYear) {
    if (validateJoinYear(joinYear)) {
      this.joinYear = joinYear;
    }
  }
}
```

In this way, we can protect the fields from accidental changes and misuse.

For example we could prevent setting an impossible join year. Notice that:

1. The `validateJoinYear()` method is `private`. It is an internal helper, not exposed to users.
2. Both the **constructor** and the **setter** call `validateJoinYear()`, so validation is always applied regardless of how the object is created or updated.
3. The `displayProfile()` method checks if `joinYear` is `0` (the default), to avoid printing a nonsense number of years if an invalid value was rejected.
4. The current year is read using `java.time.Year.now().getValue()` rather than hardcoded, so the calculation is always correct.

If the `joinYear` field were public, we could not prevent the user from setting an invalid value.

Create `App.java` and test this.

```java
Customer customer = new Customer("Tony Stark", 2015);
customer.setJoinYear(0);
customer.displayProfile();
customer.calculateDiscount();
```

The users of our public methods do not need to know how the method works. Even if we were to make some internal changes, the user would not be affected.

For example, if we were to change the criteria for a valid join year, we would only need to change the `validateJoinYear` method. The user would not need to change their code.

```java
private boolean validateJoinYear(int joinYear) {
  // if (joinYear < 1990 || joinYear > java.time.Year.now().getValue()) {
  if (joinYear < 2000 || joinYear > java.time.Year.now().getValue()) {
    System.out.println("Invalid join year.");
    return false;
  }
  return true;
}
```

---

## Part 2: Inheritance

### What is Inheritance?

Inheritance is organizing code into a parent-child hierarchy so that the child can inherit the properties and methods of the parent.

Take for example, a `Customer` class. It is the base class of a hierarchy of classes for other child classes such as `RetailCustomer`, `CorporateCustomer`, `WholesaleCustomer` and so on.

### Creating a child class

Let's create a `RetailCustomer` class that inherits from the `Customer` class. To do that, we use the `extends` keyword.

By inheriting from the `Customer` class, the `RetailCustomer` class will inherit all the fields and methods of the `Customer` class. The `Customer` class is also known as the **parent** class or **super** class. The `RetailCustomer` class is also known as the **child** class or **sub** class.

In addition, we want to make `RetailCustomer` different by declaring fields and methods specific to it.

```java
public class RetailCustomer extends Customer {
  private int accountNumber;
  private String membershipTier;
  private ArrayList<Double> purchases;

  public RetailCustomer(int accountNumber, String membershipTier) {
    // Note: We will improve this constructor later to accept name and joinYear
    // and pass them to the Customer constructor using super(name, joinYear).
    this.accountNumber = accountNumber;
    this.membershipTier = membershipTier;
    this.purchases = new ArrayList<Double>();
  }

  // add getters and setters

  // Note: We do not have a setPurchases() method here.
  // purchases is an ArrayList and we do not want to replace the entire list.
  // Instead, we use addPurchase() to add one purchase at a time.
  // This gives us better control over the data.

  // add methods specific to RetailCustomer
  public void addPurchase(double amount) {
    this.purchases.add(amount);
  }

  public double getAveragePurchase() {
    if (this.purchases.size() == 0) {
      System.out.println("No purchases recorded.");
      return 0;
    }
    double sum = 0;
    for (double amount : this.purchases) {
      sum += amount;
    }
    return sum / this.purchases.size();
  }
}
```

Note that a class may only extend from one parent class as Java does not support multiple inheritance.

```java
// ❌ This is not allowed
public class RetailCustomer extends Customer, Person {
  // ...
}
```

Now we can test our `RetailCustomer` class.

```java
public class App {
  public static void main(String[] args) {
    // Instantiating a RetailCustomer object
    RetailCustomer retail = new RetailCustomer(5001, "Gold");

    // Calling methods from the Customer (Parent) class
    retail.setName("Tony");
    retail.setJoinYear(2015);
    retail.displayProfile();
    retail.calculateDiscount();

    // Calling methods from the RetailCustomer (Child) class
    System.out.println("Membership tier: " + retail.getMembershipTier());
    retail.addPurchase(80);
    retail.addPurchase(90);
    System.out.println("Average purchase is " + retail.getAveragePurchase());
  }
}
```

The `RetailCustomer` instance has access to all the `public` methods of the `Customer` class.

Currently, we have to manually initialize the `name` and `joinYear` fields using the setter methods because they are in the `Customer` class. We can improve this by calling a constructor that accepts those values as well.

But how do we initialize these values since the `RetailCustomer` class does not have these fields?

### `super` keyword

The `super` keyword is like `this` but it refers to the parent class. It can be used to call the parent class constructor. Recall that the parent class is also known as the **super** class, hence that is what calling `super()` does.

Currently we are not using `super` yet but the `RetailCustomer` class has default values for the `name` and `joinYear` fields. This is because Java automatically called `super()` for us implicitly.

```java
public RetailCustomer(int accountNumber, String membershipTier) {
  super(); // This calls Customer()
  this.accountNumber = accountNumber;
  this.membershipTier = membershipTier;
  this.purchases = new ArrayList<Double>();
}
```

`super()` calls the no-argument constructor of the parent class when there are no arguments passed in. If we want to call a different constructor, we can do so by passing in the arguments. In this case we want to call the `Customer` constructor that accepts `name` and `joinYear`.

Our `RetailCustomer` constructor will also take in `name` and `joinYear` as arguments. These will be passed to the `Customer` constructor using `super(name, joinYear)`.

```java
public RetailCustomer(String name, int joinYear, int accountNumber, String membershipTier) {
  super(name, joinYear); // This calls Customer(String name, int joinYear)
  this.accountNumber = accountNumber;
  this.membershipTier = membershipTier;
  this.purchases = new ArrayList<Double>();
}
```

Note that we cannot call `super()` and `this()` in the same constructor. This is because both `super()` and `this()` must be the first statement in the constructor.

```java
public RetailCustomer(String name, int joinYear, int accountNumber, String membershipTier) {
  super(name, joinYear); // This calls Customer(String name, int joinYear)
  this(accountNumber, membershipTier); // ❌ This is not allowed
}
```

Back to our `main`, we can now pass in the `name` and `joinYear` to the `RetailCustomer` constructor.

```java
RetailCustomer retail = new RetailCustomer("Tony", 2015, 5001, "Gold");

// Calling methods from the Customer (Parent) class
// retail.setName("Tony");
// retail.setJoinYear(2015);
retail.displayProfile();
retail.calculateDiscount();

// Calling methods from the RetailCustomer (Child) class
System.out.println("Membership tier: " + retail.getMembershipTier());
retail.addPurchase(80);
retail.addPurchase(90);
System.out.println("Average purchase is " + retail.getAveragePurchase());
```

### The `protected` access modifier

In an earlier lesson we had mentioned the `protected` access modifier. The `protected` access modifier allows the field to be accessed by the child class.

Let's add another method to `RetailCustomer`.

```java
public void redeemPoints() {
  System.out.println(this.name + " redeemed loyalty points.");
}
```

Notice that we cannot access `name` directly in the `RetailCustomer` class. This is because the `name` field is declared as `private` in the `Customer` class. We can only access it using the `getName()` method.

```java
public void redeemPoints() {
  System.out.println(this.getName() + " redeemed loyalty points.");
}
```

If we want to access the `name` field directly, we can use the `protected` access modifier. The `protected` access modifier allows the field to be accessed by the child class.

```java
protected String name;
```

### 👨‍💻 Activity: Inheritance **(10 minutes)**

Create a `CorporateCustomer` class that inherits from the `Customer` class.

The `CorporateCustomer` class should have the following fields:

```java
private String companyName;
private double contractValue;
private String accountManager;
```

Example test code:

```java
CorporateCustomer corporate = new CorporateCustomer("John", 2010, "Acme Pte Ltd", 250000, "Sarah Lim");
// Calling Parent class methods
corporate.displayProfile();
corporate.calculateDiscount();
// Calling Child class methods
System.out.println("Company: " + corporate.getCompanyName());
System.out.println("Contract value: " + corporate.getContractValue());
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

Create a new `Calculator.java` file and code along.

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

Let's **overload** the `calculateDiscount()` method in the `RetailCustomer` class by accepting a promotion code.

```java
public void calculateDiscount(String promoCode) {
  System.out.println(this.getName() + " gets an extra discount with code " + promoCode + ".");
}
```

And run it in `main`.

```java
retail.calculateDiscount("NEWYEAR25");
```

### Runtime Polymorphism

Runtime polymorphism is also known as **method overriding**. It occurs when a child class overrides a method of the parent class.

It is called runtime polymorphism because the JVM determines which method to call based on the object type.

Overriding is useful because it allows us to define a method in the child class that has the same name and signature as the method in the parent class. This allows us to call the same method on different types of objects and get different results.

Let's **override** the `calculateDiscount()` method in the `RetailCustomer` class.

In the `RetailCustomer` class file, right click, "Source Action", "Override/Implement Methods...".

```java
@Override
public void calculateDiscount() {
  System.out.println(this.getName() + " gets a 5% retail discount.");
}
```

Now when we run the following code, the `calculateDiscount()` method in the `RetailCustomer` class will be called instead of the one in the `Customer` class.

```java
retail.calculateDiscount();
```

This is the practical value of overriding. Every customer type answers the same question, "what discount do you get", but each answers it differently. Calling code does not need to know which type it is holding.

#### The `@Override` annotation

The `@Override` annotation is used to indicate that a method is being overridden. It is not required but it is good practice to use it.

This is because without the annotation, if we accidentally misspell the method name or change the method signature, the compiler will not be able to detect it and we will not get any error messages.

```java
// @Override
public void calculateDiscont() {
  System.out.println(this.getName() + " gets a 5% retail discount.");
}
```

And then when we run the following code, the `calculateDiscount()` method in the `Customer` class will be called instead.

```java
Customer retail = new RetailCustomer("John", 2015, 5001, "Gold");
retail.calculateDiscount(); // John gets the standard discount.
```

#### Runtime Polymorphism with `super`

In some cases, we may want to call the parent class method from the child class, in addition to defining a new behavior in the child class.

We can call the parent class method from the child class using the `super` keyword.

```java
public class RetailCustomer extends Customer {
  @Override
  public void calculateDiscount() {
    super.calculateDiscount();
    System.out.println("Plus an extra 5% retail discount.");
  }
}
```

### 👨‍💻 Activity: Polymorphism **(5 minutes)**

Override the `calculateDiscount()` method in the `CorporateCustomer` class to give a 15% corporate discount.

Also, override the `displayProfile()` method in the `CorporateCustomer` class to show the company name, in addition to the original profile output.

> **Note:** In `displayProfile()`, call `super.displayProfile()` first rather than copying the parent's print statements. If the parent output changes later, your child class picks up the change automatically.

Test code:

```java
CorporateCustomer corporate = new CorporateCustomer("John", 2010, "Acme Pte Ltd", 250000, "Sarah Lim");
corporate.displayProfile();
corporate.calculateDiscount();
```

Example output (years shown depend on the current year):

```bash
# corporate.displayProfile() output
Customer: John, 16 years with us. Type: CorporateCustomer.
Company account: Acme Pte Ltd.
# corporate.calculateDiscount() output
John gets a 15% corporate discount.
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

In the case of our `Customer` class, we can make it an abstract class because we do not intend to instantiate it. We only want to use it as a superclass for other subclasses. In a real system there is no such thing as a generic customer. Every customer is a retail customer, a corporate customer, or some other concrete type.

We can also make the `calculateDiscount()` method `abstract` because we do not want to define the discount behaviour in the `Customer` class. Each child class must define its own.

```java
public abstract class Customer {
  // ...

  public abstract void calculateDiscount();
}
```

Once we declare `Customer` as `abstract`, it can only be used as a superclass for other classes.

Now when we try to instantiate the `Customer` class, we will get an error.

```java
Customer customer = new Customer("Tony Stark", 2015);
```

> **Note:** Once `calculateDiscount()` is abstract, every child class is forced to implement it. The compiler will not let you forget. That is the real benefit of an abstract method over an empty method body.

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

For example, we might want to have a common behaviour, say `Trackable`, for a `MobilePhone` as well as some other, entirely unrelated class. Two unrelated classes are not conceptually related, but yet we may need to implement a trackable behaviour for both. We can define a `Trackable` interface and have both classes implement it.

Create a file `LearnInterfaces.java` and code along. If you wish to do it in a single file, just omit the `public` keyword for the following interface and classes.

```java
public interface Trackable {
  // public abstract void track();
  // public and abstract are not required
  void track();

  // Variables in interfaces are implicitly public, static and final (constants)
  // They cannot be changed once set
  int MAX_TRACKING_DISTANCE = 1000; // same as: public static final int MAX_TRACKING_DISTANCE = 1000;

  // Default method - has a body, implementing class can use as-is or override (Java 8+)
  default void startTracking() {
    System.out.println("Tracking started. Max distance: " + MAX_TRACKING_DISTANCE + "km.");
  }
}
```

Any method declared in an interface is by default `public` and `abstract`. So, we do not need to specify the `public` and `abstract` keywords.

> 📝 **Note:** Variables declared in an interface are implicitly `public static final`, meaning they are **constants**. You cannot change their value. If you try to do `MAX_TRACKING_DISTANCE = 500`, you will get a compile error.

To use the `Trackable` interface, we need to implement it in the `MobilePhone` class with the `implements` keyword.

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
MobilePhone phone = new MobilePhone("iPhone 14");
phone.track();
phone.startTracking(); // uses default method from Trackable interface
```

Notice that `MobilePhone` can call `startTracking()` without implementing it. It inherits the default implementation from the interface.

A class can also **override** the default method with its own implementation:

```java
public class MobilePhone implements Trackable {
  // ...

  @Override
  public void track() {
    System.out.println("Tracking mobile phone " + this.model + ".");
  }

  // Overriding the default method with a custom implementation
  @Override
  public void startTracking() {
    System.out.println("Phone GPS tracking started for " + this.model + ".");
  }
}
```

Unlike inheritance, a class can implement multiple interfaces. Let's define a `Drivable` interface, which we will use shortly.

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

Now, the classes that implement the interface can choose to override the default method with their own implementation, or they can simply use the default implementation. We'll see `Drivable` used together with `Trackable` on the same class shortly, in the Abstraction activity below.

### 👨‍💻 Activity: Abstraction **(20 minutes)**

In this activity, you will practice working with abstract classes and interfaces.

> **Note:** This activity uses a vehicle model rather than the customer model. That is deliberate. Abstraction is not tied to one domain, and vehicles map cleanly onto multiple interfaces.

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

All vehicles are `Drivable` and `Trackable`. A `Car` should implement `FuelTank` and an `ElectricCar` should implement `BatteryPack`.

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

Inheritance defines an **IS-A** relationship. Composition defines a **HAS-A** relationship. A car **IS-A** vehicle. A car **HAS-A** engine.

Let's say we have a `Radio` class now. One way for our `Car` and `ElectricCar`, the same `Car` and `ElectricCar` classes we already built in the Abstraction activity, to have a radio is to put it in the `Vehicle` class. But not all vehicles have a radio.

Instead, we can create a `Radio` class and add it in the `Car` and `ElectricCar` classes.

We do not want to define the `Radio` in the `Vehicle` class as that would mean all child classes would have a `Radio`. We would also not want to define the `Radio` separately in every subclass that needs one, repeating the same field and constructor logic.

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

Now let's add a `Radio` field to our existing `Car` class from the Abstraction activity, the one that already `extends Vehicle` and `implements FuelTank`. Notice we are not creating a new `Car` class. We are adding to the one we already have.

```java
public class Car extends Vehicle implements FuelTank {
  private double fuelLevel;
  private Radio radio; // NEW - Car HAS-A Radio

  public Car(String make) {
    super(make);
    this.fuelLevel = 0;
    this.radio = new Radio("Sony"); // Car creates and owns its own Radio
  }

  @Override
  public void drive() {
    System.out.println(this.getMake() + " car is driving.");
  }

  @Override
  public void stop() {
    System.out.println(this.getMake() + " car is stopping.");
  }

  @Override
  public void fill() {
    this.fuelLevel = 100;
    System.out.println(this.getMake() + " fuel tank filled.");
  }

  @Override
  public double getFuelLevel() {
    return this.fuelLevel;
  }

  // NEW - delegating to the composed Radio object
  public void playRadio() {
    this.radio.play();
  }
}
```

The same applies to `ElectricCar`. Add a `Radio` field to the existing class from the Abstraction activity:

```java
public class ElectricCar extends Vehicle implements BatteryPack {
  private double charge;
  private Radio radio; // NEW - ElectricCar HAS-A Radio

  public ElectricCar(String make) {
    super(make);
    this.charge = 0;
    this.radio = new Radio("Sony");
  }

  @Override
  public void drive() {
    System.out.println(this.getMake() + " electric car is driving.");
  }

  @Override
  public void stop() {
    System.out.println(this.getMake() + " electric car is stopping.");
  }

  @Override
  public void charge() {
    this.charge = 100;
    System.out.println(this.getMake() + " battery charged.");
  }

  @Override
  public double getCharge() {
    return this.charge;
  }

  public void playRadio() {
    this.radio.play();
  }
}
```

Notice that this same `Car` class now uses inheritance (`extends Vehicle`), an interface (`implements FuelTank`), **and** composition (`Radio` field) all at once. These are not competing techniques. A single class can combine all three.

---

END