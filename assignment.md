# Assignment (Optional)

## Brief

Create a program called OOPPillarsAssignment.java and solve the following problems using inheritance, polymorphism, abstraction, and composition.

1. **Animal Hierarchy with Inheritance and Polymorphism**
   - Create an abstract class `Animal` with the following:
     - `protected` attributes: `String name`, `int age`
     - Constructor that takes `name` and `age`
     - Abstract method: `void makeSound()`
     - Concrete method: `void eat()` that prints "[name] is eating"
   - Create two child classes `Dog` and `Cat` that extend `Animal`:
     - Override the `makeSound()` method (Dog says "Woof!", Cat says "Meow!")
     - Add a unique method for each (e.g., `Dog` has `fetch()`, `Cat` has `scratch()`)
   - Create at least 2 objects of each type (Dog and Cat) and test all methods
   - Demonstrate polymorphism by creating an `Animal` array that holds both Dog and Cat objects, then loop through and call `makeSound()` on each

2. **Vehicle System with Interfaces and Composition**
   - Create an interface `Maintainable` with methods:
     - `void performMaintenance()`
     - `int getMaintenanceCount()`
   - Create a `GPS` class with:
     - `String brand`
     - Method: `void navigate()` that prints navigation information
   - Create a `Car` class that:
     - Implements the `Maintainable` interface
     - Has composition with a `GPS` object (HAS-A relationship)
     - Has attributes: `String model`, `int maintenanceCount`
     - Constructor that takes `model` and initializes GPS
     - Implement the interface methods
     - Add a method `useGPS()` that calls the GPS's `navigate()` method
   - Create at least 2 Car objects and demonstrate:
     - Performing maintenance (increment count each time)
     - Using the GPS navigation
     - Displaying maintenance count

## Submission (Optional)

- Submit the URL of the GitHub Repository that contains your work to NTU black board.
- Should you reference the work of your classmate(s) or online resources, give them credit by adding either the name of your classmate or URL.

## References
- Java: https://docs.oracle.com/javase/
- Spring Boot: https://docs.spring.io/spring-boot/docs/current/reference/html/
- PostgreSQL: https://www.postgresql.org/docs/
- OWASP: https://cheatsheetseries.owasp.org/