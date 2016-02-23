---
layout: post
title: ChromeDriver Mobile Emulation in your Selenium Scripts!
---

ChromeDriver 2.11 release has the mobile emulation support using Chrome Dev Tools. I have been waiting for this feature since long.

This feature is really handy to run your selenium tests against different mobile screen sizes with minimal setup. This does not provide you 100% emulation but atlest it will give you some fair idea about the compatibility of your application with different mobile screen size.

This is how we can emulate different devices using the Java code.

{% gist cca0ca97b76240bcaa81 %}

abc
```java
@CucumberOptions(features = "src/test/resources/com.cucumber.testng.examples/date_calculator1.feature", 
plugin = "json:target/cucumber2.json")
public class RunCukesByCompositionGrp1_Test2 {
    @Test(groups = "examples-testng", description = "Example of using TestNGCucumberRunner to invoke Cucumber")
    public void runCukes() {
        new TestNGCucumberRunner(getClass()).runCukes();
    }
}
```
