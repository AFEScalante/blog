---
author: Angel Escalante
pubDatetime: 2024-10-30
title: SOLID principles in R6 classes
slug: solid-principles-in-r6-classes
featured: false
draft: false
tags:
  - R Language
  - R6
  - OOP
description: Understanding SOLID principles for R programmers. Building maintainable code with R6 classes.
---

I don’t think I’ve ever seen a post about the SOLID principles with examples in R code. I have a lot of affection for this language, and I refuse to abandon it in my work as a data scientist. However, I’m also very interested in applying techniques I’ve used in other languages, as well as paradigms and principles that have helped me write better code. This post emerged from that idea. 💪

Here, we will explore object-oriented programming (OOP) and the SOLID principles and how they can be applied to **R6 classes**.

## Challenges for R programmers

🛑✋ For typical R programmers (including myself), adopting object-oriented programming (OOP) can pose several challenges. From my experience, here are four challenges I faced when transitioning to OOP:

1. **Paradigm Shift**: R is primarily functional and procedural, so many R programmers are not deeply familiar with OOP. Transitioning to OOP requires rethinking how they structure code, shifting from writing functions to defining classes, methods, and managing object states.
2. **Learning Curve for SOLID Principles**: Each SOLID principle requires understanding abstractions, inheritance, and dependency management. For R programmers who usually work in a functional or statistical mindset, these concepts may initially feel abstract and cumbersome.
3. **Limited Resources and Community Support**: Since R is heavily used in data science and statistics, most of its community resources focus on functional programming, data manipulation, and visualization. SOLID-based OOP patterns aren’t as commonly discussed or documented in R, so it can be challenging for programmers to find guidance or examples tailored to their use cases.
4. **Adaptation to Testable and Modular Code**: Applying SOLID principles encourages writing modular and testable code, which contrasts with the often more exploratory and iterative approach in R programming. R developers need to adapt to more disciplined coding practices, with a greater focus on design patterns, reusability, and unit testing.

By overcoming these challenges, R programmers can benefit from cleaner, more modular, and maintainable code structures. 🙂

## OOP paradigm in R 🔄

From Hadley Wickham's [Advanced R](https://adv-r.hadley.nz/) book:

> There are two main paradigms of object-oriented programming which differ in how methods and classes are related. In this book, we’ll borrow the terminology from the **Extending R** book and call these paradigms encapsulated and functional:

> In **functional OOP**, methods belong to generic functions, and method calls look like ordinary function calls: `generic(object, arg2, arg3)`. This is called functional because from the outside it looks like a regular function call, and internally the components are also functions.

> In **encapsulated OOP**, methods belong to objects or classes, and method calls typically look like `object.method(arg1, arg2)`. This is called encapsulated because the object encapsulates both data (with fields) and behaviour (with methods), and is the paradigm found in most popular languages.

R6 classes fall into the encapsulated OOP paradigm, where methods belong to objects or classes.

## What is an R6 class?

`{R6}` is a package that provides a more flexible and object-oriented programming (OOP) system for R. R6 classes are reference classes that allow you to create objects with mutable state and methods that can modify that state.

If you are unfamiliar with R6 classes, you can think of them as a way to create objects that encapsulate data and behavior. Each R6 object has its own state, which can be modified by calling methods on the object. There's a great resource out there made by Hadley Wickham, [R6: Encapsulated classes with reference semantics](https://adv-r.hadley.nz/r6.html). 🙌

Here's an example of an R6 class:

```r
library(R6)

# Define an R6 class
Person <- R6Class(
  "Person",
  public = list(
    name = NULL,
    initialize = function(name) {
      self$name <- name
    },
    greet = function() {
      cat("Hello, my name is", self$name, "\n")
    }
  )
)
```

In this example, we define a class called `Person` with a `name` field and a `greet` method. The `initialize` method is a special method that is called when a new object is created.

R6 classes can be inherited, which allows you to create a hierarchy of classes with shared behavior.

```r
# Define a subclass of Person
Employee <- R6Class(
  "Employee",
  inherit = Person,
  public = list(
    title = NULL,
    initialize = function(name, title) {
      super$initialize(name)
      self$title <- title
    },
    introduce = function() {
      cat("Hello, my name is", self$name, "and I am a", self$title, "\n")
    }
  )
)
```

In this example, we define a subclass of `Person` called `Employee` with a `title` field and an `introduce` method. The `initialize` method calls the `initialize` method of the superclass (`Person`) using the `super` keyword. This allows us to reuse the behavior of the superclass while adding new behavior in the subclass.

## What are the SOLID principles? 🤔

The SOLID principles are a set of five design principles that help developers create maintainable and scalable software. The principles were introduced by Robert C. Martin in the early 2000s and have since become a cornerstone of object-oriented design.

The SOLID principles are:

- **Single Responsibility Principle (SRP)**: A class should have only one reason to change.
- **Open/Closed Principle (OCP)**: A class should be open for extension but closed for modification.
- **Liskov Substitution Principle (LSP)**: Objects of a superclass should be replaceable with objects of a subclass without affecting the correctness of the program.
- **Interface Segregation Principle (ISP)**: A client should not be forced to implement an interface it does not use.
- **Dependency Inversion Principle (DIP)**: High-level modules should not depend on low-level modules. Both should depend on abstractions. Abstractions should not depend on details. Details should depend on abstractions.

### Single Responsibility Principle (SRP)

The Single Responsibility Principle (SRP) states that a class should have only one reason to change. In other words, a class should have only one responsibility or job. This principle helps to keep classes small and focused, making them easier to understand and maintain.

Let's see how we can apply this:

```r
# Bad Example
Report <- R6Class("Report",
  public = list(
    data = NULL,

    initialize = function(data) {
      self$data <- data
    },

    generateReport = function() {
      cat("Generating report for", self$data, "\n")
    },

    saveToFile = function() {
      cat("Saving report to file:", self$data, "\n")
      # logic to save the report to a file
    }
  )
)

report <- Report$new("Sales Data")
report$generateReport()
report$saveToFile()
```

In this example, the `Report` class has two responsibilities: generating a report and saving it to a file. This violates the SRP because the class has more than one reason to change. If we need to change how the report is generated or saved, we would have to modify the `Report` class, which goes against the SRP.

To apply the SRP, we can split the `Report` class into two classes: `ReportGenerator` and `ReportSaver`. Each class will have a single responsibility, making the code easier to understand and maintain.

```r
# Good Example
ReportGenerator <- R6Class("ReportGenerator",
  public = list(
    data = NULL,

    initialize = function(data) {
      self$data <- data
    },

    generateReport = function() {
      cat("Generating report for", self$data, "\n")
    }
  )
)

ReportSaver <- R6Class("ReportSaver",
  public = list(
    data = NULL,

    initialize = function(data) {
      self$data <- data
    },

    saveToFile = function() {
      cat("Saving report to file:", self$data, "\n")
      # logic to save the report to a file
    }
  )
)
```

By splitting the `Report` class into two classes, we adhere to the SRP and make the code more maintainable. Each class has a single responsibility, which makes it easier to understand and modify.

### Open/Closed Principle (OCP)

The Open/Closed Principle (OCP) states that a class should be open for extension but closed for modification. In other words, you should be able to add new functionality to a class without changing its existing code.

Let's see this in action:

```r
# Bad Example
Circle <- R6Class("Circle",
  public = list(
    radius = NULL,

    initialize = function(radius) {
      self$radius <- radius
    },

    area = function() {
      pi * self$radius^2
    }
  )
)

Square <- R6Class("Square",
  public = list(
    side = NULL,

    initialize = function(side) {
      self$side <- side
    },

    calculateArea = function() {
      return(self$side ^ 2)
    }
  )
)

circle <- Circle$new(5)
square <- Square$new(4)

circle$calculateArea()  # 78.54
square$calculateArea()  # 16
```

In this example, we have two classes, `Circle` and `Square`, that calculate the area of a circle and a square, respectively. The `Circle` class has a method `area` that calculates the area of a circle, while the `Square` class has a method `calculateArea` that calculates the area of a square.

If we want to add a new shape, such as a triangle, we would have to modify the existing classes to add a new method for calculating the area of a triangle. This violates the OCP because we have to modify the existing code to add new functionality.

To adhere to the OCP, we can create a base class `Shape` with a method `area` that is overridden by subclasses:

```r
# Good Example
Shape <- R6Class("Shape",
  public = list(
    area = function() {
      stop("Method 'area' must be overridden in subclasses")
    }
  )
)

Circle <- R6Class("Circle",
  inherit = Shape,
  public = list(
    radius = NULL,

    initialize = function(radius) {
      self$radius <- radius
    },

    area = function() {
      pi * self$radius^2
    }
  )
)

Square <- R6Class("Square",
  inherit = Shape,
  public = list(
    side = NULL,

    initialize = function(side) {
      self$side <- side
    },

    area = function() {
      self$side ^ 2
    }
  )
)

Triangle <- R6Class("Triangle",
  inherit = Shape,
  public = list(
    base = NULL,
    height = NULL,

    initialize = function(base, height) {
      self$base <- base
      self$height <- height
    },

    area = function() {
      0.5 * self$base * self$height
    }
  )
)

circle <- Circle$new(5)
square <- Square$new(4)
triangle <- Triangle$new(3, 4)

circle$area()    # 78.54
square$area()    # 16
triangle$area()  # 6
```

In this example, we create a base class `Shape` with a method `area` that throws an error if it is called. Subclasses `Circle`, `Square`, and `Triangle` inherit from `Shape` and override the `area` method with their own implementation. This allows us to add new shapes without modifying the existing code, adhering to the OCP.

### Liskov Substitution Principle (LSP)

The Liskov Substitution Principle (LSP) states that objects of a superclass should be replaceable with objects of a subclass without affecting the correctness of the program. In other words, a subclass should be able to substitute for its superclass without breaking the program.

Let's see this in action:

```r
# Bad Example
Bird <- R6Class("Bird",
  public = list(
    fly = function() {
      cat("Fly 🦅\n")
    }
  )
)

Penguin <- R6Class("Penguin",
  inherit = Bird,
  public = list(
    fly = function() {
      stop("I can't fly 🐧\n")
    }
  )
)
```

In this example, we have a `Bird` class with a `fly` method and a `Penguin` class that inherits from `Bird` and overrides the `fly` method that throws an error. This violates the LSP because a `Penguin` object cannot substitute for a `Bird` object without breaking the program.

To adhere to the LSP, we can create a `swim` method in the `Penguin` class instead of overriding the `fly` method:

```r
# Good Example
Bird <- R6Class("Bird",
  public = list(
    fly = function() {
      cat("Fly 🦅\n")
    }
  )
)

Penguin <- R6Class("Penguin",
  inherit = Bird,
  public = list(
    swim = function() {
      cat("Swim 🐧\n")
    }
  )
)

bird <- Bird$new()
penguin <- Penguin$new()

bird$fly()     # Fly 🦅
penguin$swim() # Swim 🐧
penquin$fly()  # Fly 🦅
```

This allows a `Penguin` object to substitute for a `Bird` object without breaking the program, adhering to the LSP.

### Interface Segregation Principle (ISP)

The Interface Segregation Principle (ISP) states that a client should not be forced to implement an interface it does not use. In other words, interfaces should be small and focused on a single responsibility.

Let's see this:

```r
# Bad Example
BankAccount <- R6Class("BankAccount",
  public = list(
    balance = 0,

    initialize = function(balance) {
      self$balance <- balance
    },

    deposit = function(amount) {
      self$balance <- self$balance + amount
    },

    withdraw = function(amount) {
      self$balance <- self$balance - amount
    },

    apply_interest = function() {
      self$balance <- self$balance * 1.05
    }
  )
)
```

In this example, the `BankAccount` class has four methods: `deposit`, `withdraw` and `apply_interest`. The `apply_interest` method is not used by all clients of the `BankAccount` class, which violates the ISP.

To adhere to the ISP, we can implement a `BasicBankAccount` class with only the `deposit` and `withdraw` methods, and a `SavingsAccount` class with the `apply_interest` method:

```r
# Good Example
BasicBankAccount <- R6Class("BasicBankAccount",
  public = list(
    balance = 0,

    initialize = function(balance) {
      self$balance <- balance
    },

    deposit = function(amount) {
      self$balance <- self$balance + amount
    },

    withdraw = function(amount) {
      self$balance <- self$balance - amount
    }
  )
)

SavingsAccount <- R6Class("SavingsAccount",
  inherit = BasicBankAccount,
  public = list(
    apply_interest = function() {
      self$balance <- self$balance * 1.05
    }
  )
)
```

### Dependency Inversion Principle (DIP)

The Dependency Inversion Principle (DIP) states that high-level modules should not depend on low-level modules. Both should depend on abstractions.
This principle, along with the Liskov Substitution Principle (LSP), were the most challenging for me to understand when I started learning about SOLID principles. And there's still discussion about how to apply them.

```r
# Bad Example
BankAccountWithCommission <- R6Class("BankAccountWithCommission",
  public = list(
    balance = 0,
    commission_type = "fixed",  # internally defines the type of commission

    deposit = function(amount) {
      if (self$commission_type == "fixed") {
        commission <- 5  # Fixed commission of 5
      } else if (self$commission_type == "percentage") {
        commission <- amount * 0.02  # 2% commission
      } else {
        commission <- 0  # No commission if type is unspecified
      }
      self$balance <- self$balance + amount - commission
    }
  )
)
```

In this example, the `BankAccountWithCommission` class has a `deposit` method that calculates a commission based on the `commission_type` field. This violates the DIP because the class depends on low-level details (the commission calculation logic) and is not easily extensible.

To adhere to the DIP, we can create a `CommissionCalculator` class that calculates the commission and inject it into the `BankAccountWithCommission` class:

```r
# Good Example
BankAccountWithCommission <- R6Class("BankAccountWithCommission",
  public = list(
    balance = 0,
    commission_strategy = NULL,

    initialize = function(commission_strategy) {
      self$commission_strategy <- commission_strategy
    },

    deposit = function(amount) {
      commission <- self$commission_strategy$calculate_commission(amount)
      self$balance <- self$balance + amount - commission
    }
  )
)

CommissionStrategy <- R6Class("CommissionStrategy",
  public = list(
    calculate_commission = function(amount) {
      stop("This method must be implemented by a subclass")
    }
  )
)

FixedCommission <- R6Class("FixedCommission",
  inherit = CommissionStrategy,

  public = list(
    calculate_commission = function(amount) {
      return(5)  # Fixed commission of 5
    }
  )
)

PercentageCommission <- R6Class("PercentageCommission",
  inherit = CommissionStrategy,

  public = list(
    calculate_commission = function(amount) {
      return(amount * 0.02)  # 2% commission
    }
  )
)
```

In this example, we create a `CommissionStrategy` class with a `calculate_commission` method that throws an error if called. Subclasses `FixedCommission` and `PercentageCommission` inherit from `CommissionStrategy` and override the `calculate_commission` method with their own implementation. The `BankAccountWithCommission` class takes a `commission_strategy` object as a parameter and uses it to calculate the commission, adhering to the DIP.

## Conclusion

Understanding the SOLID principles is essential for writing maintainable and scalable code in R. By following these principles, you can create classes that are easier to understand, modify, and extend. The examples in this post demonstrate how the SOLID principles can be applied to R6 classes, but they can be applied to any object-oriented programming language.

I hope this post has helped you understand the SOLID principles in the R context. Thanks for reading! 🚀

## References

- [R6: Encapsulated classes with reference semantics](https://adv-r.hadley.nz/r6.html)
- [A Solid Guide to SOLID Principles](https://www.baeldung.com/solid-principles)
