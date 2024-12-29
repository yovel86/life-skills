# SOLID Principles
The SOLID principles are a set of guidelines for writing **high-quality, maintainable, and scalable software**.

It consists of 5 priciples
1) S - Single Responsibility Principle (SRP)
2) O - Open/Closed Principle (OCP)
3) L - Liskov's Substitution Principle (LSP)
4) I - Interface Segregation Principle (ISP)
5) D - Dependency Inversion Principle (DIP)

## Single Responsibility Principle
* It states that a code unit (class/method) should have only one reason to change, or in other words, **it should have only one responsibility**
* If a code unit has too many responsibilities, the code becomes hard to understand, maintain and modify
* By following SRP, we can create code that is more modular, easier to understand, and less prone to errors

```java
class Employee {

    private String name;
    private double salary;

    public Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }

    public double calculateAnnualSalary() {
        return salary * 12;
    }

    public void printDetails() {
        // printing implementation
    }

    public void saveToDb() {
        // save to database implementation
    }

}

```
* The above Employee class violates the SRP because it has multiple responsibilities – it is responsible for calculating the annual salary, printing the details, and saving the data to the database.
* As a result, if the calculation logic changes, such as the addition of taxes, the calculateAnnualSalary() method would require modification.
* Similarly, if the printing or database-saving implementation changes at any point, the class would need to be changed.

### Solution
```java
class Employee {

    private String name;
    private double salary;

    public Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }

    public double calculateAnnualSalary() {
        return salary * 12;
    }
}

class EmployeeDao {

  private Employee employee;

  public EmployeeDao(Employee employee) {
    this.employee = employee;
  }

  public void saveToDb() {
    // save to database implementation
  }

}

class EmployeePrinter {
  
  private Employee employee;

  public EmployeeDao(Employee employee) {
    this.employee = employee;
  }

  public void printDetails() {
    // print details
  }

}
```
* In this refactored example, Employee class is responsible only for calculating the annual salary, and the printing and saving responsibilities have been delegated to separate classes. 
* This makes the code more modular, easier to understand, and less prone to errors.

## Open/Closed Principle
* It states that **software entities (classes, modules, functions, and so on) should be open for extension but closed for modification.**
* This means that the behavior of a software entity can be extended without modifying its source code.
* By allowing software entities to be extended without modification, developers can add new functionality without the risk of breaking existing code.
```java
class EmployeeDao {

  private Employee employee;

  public EmployeeDao(Employee employee) {
    this.employee = employee;
  }

  public void saveToDb() {
    // save to database implementation
  }

}
```
* Suppose there's a new requirement to save the data to a file as well. One way to implement this requirement would be to modify the existing EmployeeDao class by adding a saveToFile() method. 
* But this violates the Open-Closed Principle because it modifies the existing code.
### Solution
* A better solution would be to create an InvoiceDao interface and implement it separately for database and file.
```java 
interface EmployeeDao {
    public void save(Employee employee);
}

class DatabaseEmployeeDao implements EmployeeDao {
    @Override
    public void save(Employee employee) {
        // save to database implementation
    }
}

class FileEmployeeDao implements EmployeeDao {
    @Override
    public void save(Employee employee) {
        // save to file implementation
    }
}
```
* Now we can implement a new EmployeeDao implementation without modifying the existing code.
* EmployeeDao interface is open for extension and closed for modification, which follows the OCP

## Liskov Substitution Principle
* It states that **any instance of a derived class should be substitutable for an instance of its base class without affecting the correctness of the program.**
* LSP lies in its ability to ensure that the behavior of a program remains consistent and predictable when substituting objects of different classes.
* Violating the LSP can lead to unexpected behavior, bugs, and maintainability issues.

```java 
class Bird {
    public void fly() {
        System.out.println("Flying...");
    }
}

class Sparrow extends Bird {
    @Override
    public void fly() {
        System.out.println("Sparrow flying...");
    }
}

class Penguin extends Bird {
    @Override
    public void fly() {
        throw new UnsupportedOperationException("Penguins can't fly!");
    }
}
```
* A Penguin is a Bird, but calling fly() on a Penguin breaks the program's behavior. The substitution of Penguin for Bird is invalid because Penguin cannot conform to the expected behavior of fly()
### Solution
- Use composition and interfaces to model shared and unique behaviors instead of forcing all birds to share the same methods.
  - Create an Abstract Bird Class - To represent shared characteristics like name or species.
  - Create a Flyable Interface - Define an interface for flying behavior that only flying birds implement.
  - Separate Non-Flying Behavior - Penguin does not implement the Flyable interface. Instead, it can have its own behavior, like swimming.
- Result 
  - All birds (flying or non-flying) can still inherit shared properties from the Bird class.
  - Only birds that can fly have a fly() method, avoiding violations of LSP.

## Interface Segregation Principle
* ISP suggests that instead of creating a large interface that covers all the possible methods, it's better to create smaller, more focused interfaces for specific use cases. 
* This approach results in interfaces that are more cohesive and less coupled.
```java 
interface Vehicle {
    void startEngine();
    void stopEngine();
    void drive();
    void fly();
}

class Car implements Vehicle {

    @Override
    public void startEngine() {
        // implementation
    }

    @Override
    public void stopEngine() {
        // implementation
    }

    @Override
    public void drive() {
        // implementation
    }

    @Override
    public void fly() {
        throw new UnsupportedOperationException("This vehicle cannot fly.");
    }
}
```
* In this example, the Vehicle interface has too many methods. The Car class is forced to implement all of them, even though they cannot fly. 
* This violates the ISP because the Vehicle interface is not properly segregated into smaller interfaces based on related functionality.
### Solution
* Refactor the Vehicle interface into smaller, more focused interfaces
```java 
interface Drivable {
    void startEngine();
    void stopEngine();
    void drive();
}

interface Flyable {
    void fly();
}
```
* Now, you can have a class called Car that only implements the Drivable interface.

# Dependency Inversion Principle
* DIP states that **high-level modules should not depend on low-level modules, but both should depend on abstractions**.
* This principle aims to reduce coupling between modules, increase modularity, and make the code easier to maintain, test, and extend.
```java
class Keyboard {

    public String getInput() {
        return "Keyboard Input";
    }

}

class Computer {

    private Keyboard keyboard;

    public Computer() {
        this.keyboard = new Keyboard(); // Direct dependency on concrete class
    }

    public void start() {
        String input = keyboard.getInput();
        monitor.display("Computer started with input: " + input);
    }

}
```
* The Computer class directly depends on the concrete implementations of Keyboard.
* This tight coupling makes it hard to replace or extend Keyboard with other input devices.
### Solution
```java
// Abstraction for input devices
interface InputDevice {
    String getInput();
}

// Concrete implementation for Keyboard
class Keyboard implements InputDevice {
    @Override
    public String getInput() {
        return "Keyboard Input";
    }
}

// High-level module
class Computer {

    private final InputDevice inputDevice;

    public Computer(InputDevice inputDevice, OutputDevice outputDevice) {
        this.inputDevice = inputDevice;
    }

    public void start() {
        String input = inputDevice.getInput();
        outputDevice.display("Computer started with input: " + input);
    }

}
```
* Now the Computer class depends on the abstractions InputDevice, passed via its constructor.
* You can now easily substitute Keyboard with other implementations (e.g., a TouchScreen) without changing the Computer class

**Reference: [https://www.freecodecamp.org/news/solid-principles-for-better-software-design/](https://www.freecodecamp.org/news/solid-principles-for-better-software-design/)**