---
title: "Code Style"
date: 2024-09-03
excerpt: ""
collection: sat
---


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

Note that the last line of code is not part of the body of the last ```if``` statement and in fact, gets executed every time the method ```authorize``` is invoked. For this particular application, this goes beyond correctness and presents a high security risk. 