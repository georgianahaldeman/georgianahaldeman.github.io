---
title: "Intro to SAT"
date: 2024-08-30
excerpt: "First Class"
collection: sat
---


### Software Engineering


Common job descriptions for someone who writes code are programmer, software developer, or software engineer. They are all describing the same job. The variety in names hints at the similarities and differences between software engineering and other engineering. Engineering aims to build something that serves a function, a product, or an artifact. 


Most engineering activities involve a design that is separately developed from the product. There are three inter-related main ways in which software engineering differs:
1. It is malleable (the product can be changed without necessarily changing the design)
2. It is human-intensive (in regular engineering, the product development involves manufacturing, most often mechanized with little to no involvement from humans)
3. The quality of the product is more often than not intermixed with that of the design. The right process helps to produce the right product and vice versa.


In fact, it’s more common to hear about the software process vs. the software product in software engineering than design vs. product.


### Software Quality

Software Analysis and Testing are very closely related to Software Quality. There are many desirable software qualities in software engineering: correctness, reliability (relative correctness), robustness (reasonable behavior even in circumstances that were not anticipated in the requirements), performance, usability (ease of use), verifiability (we will cover testing in this course), security, maintainability, reusability, portability, understandability (although some applications are less clear than others the design and implementation should not further affect it), interoperability, productivity, timeliness, visibility (transparency of the process, amenable for external examination).


When addressing software quality it’s also useful to consider the different parties: users, developers, and managers because some of the software qualities are visible to the user (we call these external qualities) and some are only visible to the developers of the system (we call these internal qualities). A user wants a product to be reliable, efficient, and easy to use. The developer wants it to be verifiable, maintainable, portable, and extensible, the manager wants the process to be productive, predictable, and easy to control. 


Furthermore, while the process is for the most part not visible to the user, the product has user-visible components such as executable code and user manual, and intermediate components (work products or artifacts) such as requirements, design documents, test data, etc. These intermediate components can also be visible to other types of users (other than the end-users). 


We conclude that there is a high overlap between external and internal qualities, product and process qualities which makes it hard to standardize it. There are several software quality models that share the same essential aspects but differ in emphasis and organization. 


### Software Quality Principles

Some general principles to achieve the software qualities discussed prior are:
- **Rigor and Formality**. Rigor means precision and exactness. Formality is a stronger requirement than rigor which requires the use of mathematical laws to evaluate and proceed. It’s impossible to achieve formality in software engineering because of its creative nature so the best we can strive for in many instances is rigor. We will briefly study some formal methods in this course. However, one crucial expectation of the course is that you will practice rigor in your software engineering activities. For example, many students coming into this course are very intuitive about their testing practices. In a few weeks, we will discuss the differences between intuitive and systematic testing. We will then learn different systematic and effective methods to engage in testing. Note that although in the past you were assessed based on the end product (the tests you provided), in this course you will be primarily assessed on the rigor of the process and the reporting.
- Separations of concerns
- Modularity
- Abstraction
- Incrementality 
- Anticipation of change and generality 

### Code Style


Code style is connected to many desirable software qualities of code, including correctness. Let's look at this code:

```Java
class BoardComputer {

    CruiseControl cruiseControl;

    void authorize(User user) {
        Objects.requireNonNull(user);
        if (user.isUnknown())
            cruiseControl.logUnauthorizedAccessAttempt();
        if (user.isAstronaut())
            cruiseControl.grantAccess(user);
        if (user.isCommander())
            cruiseControl.grantAccess(user);
            cruiseControl.grantAdminAccess(user);
    }
}
```

Note that the last line of code is not part of the body of the last ```if``` statement and in fact, gets executed every time the method ```authorize``` is invoked. For this particular application, this goes beyond correctness and presents a high security risk. The fix is to add curly braces around the body like so:

```Java
class BoardComputer {

    CruiseControl cruiseControl;

    void authorize(User user) {
        Objects.requireNonNull(user);
        if (user.isUnknown()){
            cruiseControl.logUnauthorizedAccessAttempt();
        }
        if (user.isAstronaut()){
            cruiseControl.grantAccess(user);
        }
        if (user.isCommander()){
            cruiseControl.grantAccess(user);
            cruiseControl.grantAdminAccess(user);
        }
    }
}
```

One may also conclude that the three cases are mutually exclusive and combine them in an ```if-else-if``` structure like so:

```Java
class BoardComputer {

    CruiseControl cruiseControl;

    void authorize(User user) {
        Objects.requireNonNull(user);
        if (user.isUnknown()){
            cruiseControl.logUnauthorizedAccessAttempt();
        }
        else if (user.isAstronaut()){
            cruiseControl.grantAccess(user);
        }
        else if (user.isCommander()){
            cruiseControl.grantAccess(user);
            cruiseControl.grantAdminAccess(user);
        }
    }
}
```

However, note that there is an asymmetry in the code from the fact that we are mixing authorizing code with non-authorizing code. We can increase the understandability of our code by separating them out like so:

```Java
class BoardComputer {

    CruiseControl cruiseControl;

    void authorize(User user) {
        Objects.requireNonNull(user);
        if (user.isUnknown()) {
            cruiseControl.logUnauthorizedAccessAttempt();
            return;
        }

        if (user.isAstronaut()) {
            cruiseControl.grantAccess(user);
        } else if (user.isCommander()) {
            cruiseControl.grantAccess(user);
            cruiseControl.grantAdminAccess(user);
        }
    }
} 
```

[Code Style Worksheet](8_30.pdf)