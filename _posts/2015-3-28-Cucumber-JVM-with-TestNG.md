---
layout: post
title: Integrating Cucumber-JVM with TestNG
---

Cucumber-JVM has a native integration with JUnit to run the BDD scenarios. Junit is a good framework which is more popular among developers to support their unit testing. When we talk about function automation testing then TestNG is a better choice to use as a test harness as it has more advanced features. Detailed comparison between these two frameworks can be found [here](http://www.mkyong.com/unittest/junit-4-vs-testng-comparison/)

In this blog I will be taking more about the Cucumber-JVM and TestNG Integration. With JUnit, the integration is quite straight forward, we simply need to create a test runner class like this.

```java
package com.cucumber.testng.examples;

import cucumber.api.CucumberOptions;
import cucumber.api.junit.Cucumber;
import org.junit.runner.RunWith;

@RunWith(Cucumber.class)
@CucumberOptions(plugin = "json:target/cucumber-report.json")
public class RunCukesTest {
}
```

Contrary to JUnit, TestNG integration is not that straigh forward. 
**I have created a [sample project](https://github.com/sahajamit/cucumber-jvm-testng-integration) on Github to demonstrate this integration and can be referred to see the complete code.**
There are three ways to integrate your cucumber tests with TestNG. I will cover all three in detail along with its benefits. 

# 1. Using AbstractTestNGCucumberTests Class #

This is more like JUnit Integration where we are creating a simple "RunCukesTest" class and extending it with AbstractTestNGCucumberTests. Unlike JUnit, here there is no need to specify the test runner explicitly as be default AbstractTestNGCucumberTests class will use TestNG to execute the scenarios. 

**Here is the sample code.**

```java
package com.cucumber.testng.examples;

import cucumber.api.CucumberOptions;
import cucumber.api.testng.AbstractTestNGCucumberTests;

@CucumberOptions(features = "src/test/resources/com.cucumber.testng.examples/date_calculator1.feature", format = { "pretty",
        "html:target/site/cucumber-pretty",
        "rerun:target/rerun.txt",
        "json:target/cucumber1.json" })
public class RunCukesTest extends AbstractTestNGCucumberTests {
}
```

The CucumberOptions can be used same as JUnit. In the above example we are pointing to a specific feature file but here we can also point to a folder which has all the feature files. 

# 2. Run scenarios as composition test: # 
In this approach the test does not inherit from AbstractTestNGCucumberTests. We will create one test method in this class, that will be invoked by TestNG and invoke the cucumber runner within that method. In this approach we can easily call this testng method from testng.xml and can also use maven to trigger the execution.

**Here is the sample code.**

```java
package com.cucumber.testng.examples;

import cucumber.api.CucumberOptions;
import cucumber.api.testng.TestNGCucumberRunner;
import org.testng.annotations.Test;

@CucumberOptions(plugin = "json:target/cucumber-report-composite.json")
public class RunCukesByCompositionTest extends RunCukesByCompositionBase {

    @Test(groups = "examples-testng", description = "Example of using TestNGCucumberRunner to invoke Cucumber")
    public void runCukes() {
        new TestNGCucumberRunner(getClass()).runCukes();
    }
}
```

# 3. Run scenarios as feature and composition test: # 
In this approach test class does not inherit from AbstractTestNGCucumberTests but still executes each feature as a separate TestNG test. Here we have leveraged the TestNG DataProvider feature which can read all the feature files in file directory and provide it to the testng method. This way each cucumber feature can be executed as an independent TestNg test.

**Here is the sample code.**

```java
package com.cucumber.testng.examples;

import cucumber.api.CucumberOptions;
import cucumber.api.testng.TestNGCucumberRunner;
import cucumber.api.testng.CucumberFeatureWrapper;
import org.testng.annotations.AfterClass;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;

@CucumberOptions(features = "src/test/resources/com.cucumber.testng.examples/date_calculator1.feature", plugin = "json:target/cucumber1.json")
public class RunCukesByFeatureAndCompositionTest1 {
    private TestNGCucumberRunner testNGCucumberRunner;

    @BeforeClass(alwaysRun = true)
    public void setUpClass() throws Exception {
        testNGCucumberRunner = new TestNGCucumberRunner(this.getClass());
    }

    @Test(groups = "cucumber", description = "Runs Cucumber Feature", dataProvider = "features")
    public void feature(CucumberFeatureWrapper cucumberFeature) {
        testNGCucumberRunner.runCucumber(cucumberFeature.getCucumberFeature());
    }

    @DataProvider
    public Object[][] features() {
        return testNGCucumberRunner.provideFeatures();
    }

    @AfterClass(alwaysRun = true)
    public void tearDownClass() throws Exception {
        testNGCucumberRunner.finish();
    }
}
```
# Executing the Cucumber Feature Files in Prallel. #

To Execute the cucumber features in parallel we can leverage the TestNg multi-threading feature. To accomplish this We can devide the all the feature files in multiple directories and then we can create multiple FeatureAndCompositionTest classes where each class is pointing to a specific feature folder. 
**The following example can make this more clear.**

Here are the two features files I have to run parallelly.

![_config.yml]({{ site.baseurl }}/images/features.png)

