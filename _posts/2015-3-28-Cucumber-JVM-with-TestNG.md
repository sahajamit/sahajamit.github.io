---
layout: post
title: Integrating Cucumber-JVM with TestNG
---

Cucumber-JVM has a native integration with JUnit to run the BDD scenarios. Junit is good framework which is more popular among developers to support their unit testing. When we talk about function automation testing then TestNG is a better choice to use as a test harness as it has more advanced features. Detailed comparison between these two frameworks can be found [here](http://www.mkyong.com/unittest/junit-4-vs-testng-comparison/)

In this blog I will be taking more about the Cucumber-JVM and TestNG Integration.

```java
package com.cucumber.testng.examples;

import cucumber.api.Scenario;
import cucumber.api.java.After;
import cucumber.api.java.Before;

/**
 * Created by amit.rawat on 21/12/15.
 */
public class BaseStepDefs {
    @Before()
    public void before(Scenario scenario) {
        scenario.getId();
        System.out.println("This is before Scenario: " + scenario.getName().toString());
    }

    @After
    public void after(Scenario scenario) {
        System.out.println("This is after Scenario: " + scenario.getName().toString());
    }
}
```

