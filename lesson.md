# Lesson: Object-Oriented Programming — Inheritance, Polymorphism, Abstraction, Composition

## Lesson Overview

This lesson builds on the previous lesson to complete the four pillars of OOP. Encapsulation was covered in depth in the previous lesson, so today focuses on the remaining three pillars, inheritance, polymorphism, and abstraction, plus composition. You will grow the `Customer` class into a hierarchy of customer types, which is the same shape of model you will later expose through a REST API.

## Lesson Objectives

- Apply inheritance using `extends`, `super`, and `protected` to build class hierarchies.
- Apply method overriding to achieve runtime polymorphism.
- Design abstractions using abstract classes and interfaces.
- Model HAS-A relationships using composition.

## Object Oriented Programming

Object Oriented Programming (OOP) is based on 4 principles:

- Encapsulation (covered in the previous lesson)
- Inheritance
- Polymorphism
- Abstraction

## Part 1: The Starting Point — The Customer Class

Create a new folder for this lesson. We will rebuild `Customer` from scratch as a simpler version with just two fields, because today is about how classes relate to each other, not about the class itself.

This class applies everything from the previous lesson: private fields, validation, and a private helper method.

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

Quick notes, all of this is applying the previous lesson:

1. `validateJoinYear()` is `private`. It is an internal helper, not part of the public interface.
2. Both the constructor and the setter call it, so validation applies on every path in.
3. `displayProfile()` checks whether `joinYear` is `0` (the default) to avoid printing a nonsense number of years.
4. The current year is read using `java.time.Year.now().getValue()` so the calculation is always correct.

Create `App.java` and test it.

```java
Customer customer = new Customer("Tony Stark", 2015);
customer.setJoinYear(0);
customer.displayProfile();
customer.calculateDiscount();
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

retail.displayProfile();
retail.calculateDiscount();

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

---

## Part 3: Polymorphism

### What is Polymorphism?

Polymorphism means many forms i.e. the ability of a method to have different behaviours in different situations.

There are 2 types of polymorphism:

1. Static or compile-time polymorphism, which is **method overloading**. You have already used this with methods and with constructors, so we will only recap it briefly.
2. Dynamic or run-time polymorphism, which is **method overriding**. This is the focus of this part.

### Quick recap: Method Overloading

Overloading is multiple methods with the same name but different parameters. The compiler decides which one to call based on the arguments. As a quick example on our existing class, we can overload `calculateDiscount()` in `RetailCustomer` to accept a promotion code.

```java
public void calculateDiscount(String promoCode) {
  System.out.println(this.getName() + " gets an extra discount with code " + promoCode + ".");
}
```

```java
retail.calculateDiscount("NEWYEAR25");
```

Same method name, different parameter list. That is all overloading is. Now on to the important one.

### Runtime Polymorphism: Method Overriding

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

For example, animals, vehicles, products, etc. are all abstract concepts. We know examples of vehicles such as cars, vans, lorries, buses but it would be hard to draw a "vehicle" without knowing what type of vehicle it is. So a vehicle is an abstract concept.

We can do abstraction in Java using:

- Abstract classes
- Interfaces

### Abstract Classes

An abstract class is a class that is declared with the `abstract` keyword. It can have abstract and non-abstract methods.

An abstract method is a method that is declared without an implementation. It is declared with the `abstract` keyword. The implementation **must** be provided by the subclass.

An abstract class cannot be instantiated. It can only be used as a superclass for other classes.

In the case of our `Customer` class, we can make it an abstract class because we do not intend to instantiate it. In a real system there is no such thing as a generic customer. Every customer is a retail customer, a corporate customer, or some other concrete type.

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

Interfaces allow us to specify what a class must do, without specifying how it should do it.

Using interfaces allows us to define a common behaviour that can be shared among multiple classes, even classes that are completely unrelated to each other.

For example, we might want a common behaviour, say `Trackable`, for a `MobilePhone` as well as some other, entirely unrelated class. We can define a `Trackable` interface and have both classes implement it.

Create a file `LearnInterfaces.java` and code along. If you wish to do it in a single file, just omit the `public` keyword for the following interface and classes.

```java
public interface Trackable {
  // public abstract void track();
  // public and abstract are not required
  void track();

  // Variables in interfaces are implicitly public, static and final (constants)
  int MAX_TRACKING_DISTANCE = 1000; // same as: public static final int MAX_TRACKING_DISTANCE = 1000;

  // Default method - has a body, implementing class can use as-is or override (Java 8+)
  default void startTracking() {
    System.out.println("Tracking started. Max distance: " + MAX_TRACKING_DISTANCE + "km.");
  }
}
```

Any method declared in an interface is by default `public` and `abstract`, so we do not need to specify those keywords.

To use the `Trackable` interface, we implement it in the `MobilePhone` class with the `implements` keyword.

```java
public class MobilePhone implements Trackable {
  private String model;

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

Notice that `MobilePhone` can call `startTracking()` without implementing it. It inherits the default implementation from the interface. A class can also **override** a default method with its own implementation.

Unlike inheritance, a class can implement multiple interfaces. We will use that in the activity below.

### When to use which

Use an **interface** when you want to define a contract that any class can implement, regardless of its position in the class hierarchy, or when a class needs multiple behaviours (a class can implement many interfaces but extend only one class).

Use an **abstract class** when you want to provide a common base implementation and shared fields for a group of closely related classes.

### 👨‍💻 Activity: Abstraction **(20 minutes)**

In this activity, you will practice working with abstract classes and interfaces.

> **Note:** This activity uses a vehicle model rather than the customer model. That is deliberate. Abstraction is not tied to one domain, and vehicles map cleanly onto multiple interfaces.

Create an `abstract` class `Vehicle`. Create a child class `Car` that extends the `Vehicle` class.

```java
public abstract class Vehicle {
  private String make;

  // Add your constructors, getters and setters
}
```

There are 4 interfaces:

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

All vehicles are `Drivable` and `Trackable`. A `Car` should also implement `FuelTank`.

Test code:

```java
Car car = new Car("Toyota");
car.track();
car.drive();
car.stop();
car.fill();
car.getFuelLevel();
```

### Optional challenge

Create an `ElectricCar` class that extends `Vehicle` and implements `Drivable`, `Trackable` and `BatteryPack`.

Test code:

```java
ElectricCar electricCar = new ElectricCar("Tesla");
electricCar.track();
electricCar.drive();
electricCar.stop();
electricCar.charge();
electricCar.getCharge();
```

---

## Part 5: Composition

Another concept of OOP is composition. Building a class out of simpler classes is called composition.

Inheritance defines an **IS-A** relationship. Composition defines a **HAS-A** relationship. A car **IS-A** vehicle. A car **HAS-A** engine.

To prefer **composition over inheritance** is a well known OOP principle. Inheritance is a tightly coupled relationship, if the superclass changes, every subclass is affected. Composition is loosely coupled, if the composed class changes internally, the class that uses it is not affected.

For more info: https://en.wikipedia.org/wiki/Composition_over_inheritance

Let's say we have a `Radio` class. Not all vehicles have a radio, so we should not put it in the `Vehicle` class. Instead, we add a `Radio` field to the `Car` class.

```java
public class Radio {
  private String model;

  public Radio(String model) {
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

Now we add a `Radio` field to our existing `Car` class from the Abstraction activity, the one that already `extends Vehicle` and `implements FuelTank`. Notice we are not creating a new `Car` class. We are adding to the one we already have.

```java
public class Car extends Vehicle implements FuelTank {
  private double fuelLevel;
  private Radio radio; // NEW - Car HAS-A Radio

  public Car(String make) {
    super(make);
    this.fuelLevel = 0;
    this.radio = new Radio("Sony"); // Car creates and owns its own Radio
  }

  // ... existing drive(), stop(), fill(), getFuelLevel() methods stay the same

  // NEW - delegating to the composed Radio object
  public void playRadio() {
    this.radio.play();
  }
}
```

Notice that this same `Car` class now uses inheritance (`extends Vehicle`), an interface (`implements FuelTank`), **and** composition (`Radio` field) all at once. These are not competing techniques. A single class can combine all three.

> **Note:** Composition is the shape of every Spring application you will build later in this module. A service class holds a repository as a field, a controller holds a service as a field. That is composition, a class built out of other classes it HAS, rather than classes it IS.

---

END