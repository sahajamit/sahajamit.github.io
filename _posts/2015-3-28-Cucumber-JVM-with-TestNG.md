---
layout: post
title: Integrating Cucumber-JVM with TestNG
---

Cucumber-JVM has a native integration with JUnit to run the BDD scenarios. Junit is a framework which is more popular among developers to support their unit testing. When we talk about function automation testing then TestNG is a better choice as it has more advanced features.In this blog I will be taking more about the Cucumber-JVM and TestNG Integration. 

With JUnit, the integration is quite straight forward, we simply need to create a test runner class like this.

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
# Executing the Cucumber Feature Files in Prallel using TestNG. #

To Execute the cucumber features in parallel we can leverage the TestNg multi-threading feature. To accomplish this We can devide the all the feature files in multiple directories and then we can create multiple FeatureAndCompositionTest classes where each class is pointing to a specific feature folder. 
**The following example can make this more clear.**

Here are the two features files I have to run parallelly.

![_config.yml]({{ site.baseurl }}/images/features.png)

Now I will be creating two FeatureAndCompositionTest classes pointing to each of the above features. Here is the code for these classes.

**RunCukesByFeatureAndCompositionTest1** 

```java
package com.cucumber.testng.examples;

import cucumber.api.CucumberOptions;
import cucumber.api.testng.TestNGCucumberRunner;
import cucumber.api.testng.CucumberFeatureWrapper;
import org.testng.annotations.AfterClass;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
/**
 * Created by 1531411 on 3/29/2016.
 */
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

**RunCukesByFeatureAndCompositionTest1** 

```java
package com.cucumber.testng.examples;

import cucumber.api.CucumberOptions;
import cucumber.api.testng.CucumberFeatureWrapper;
import cucumber.api.testng.TestNGCucumberRunner;
import org.testng.annotations.AfterClass;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;

/**
 * Created by 1531411 on 3/29/2016.
 */
@CucumberOptions(features = "src/test/resources/com.cucumber.testng.examples/date_calculator2.feature", plugin = "json:target/cucumber2.json")
public class RunCukesByFeatureAndCompositionTest2 {
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
**Invoking the execution from testng.xml**
One more important thing to be noticed here that in the CucumberOptions we have given different names to the cucmber results json file. This way the results for these two chuck of features files will be saved separately. Now we will execute these two features using TestNG in parallel. Here is the testng.xml which will will invoke two threads and run these features independently.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd">
<suite name="Custom suite" verbose="1" thread-count="1" parallel="tests" configfailurepolicy="continue">
    <listeners>
        <listener class-name="com.cucumber.testng.examples.TestNGExecutionListener"></listener>
    </listeners>
    <test name="Cucumber Date Calculator Test 1" annotations="JDK" preserve-order="true">
        <classes>
            <class name="com.cucumber.testng.examples.RunCukesByFeatureAndCompositionTest1"/>
        </classes>
    </test>
    <test name="Cucumber Date Calculator Test 2" annotations="JDK" preserve-order="true">
        <classes>
            <class name="com.cucumber.testng.examples.RunCukesByFeatureAndCompositionTest2"/>
        </classes>
    </test>
</suite>
```

## Test Reporting ##
In this approach it would be difficult to use the cucumber's native pretty html reports as we are generating two json files (cucumber1.json and cucumber2.json). So to merge these reports in a single HTML I have decided to go with [Masterthought](https://github.com/damianszczepanik/cucumber-reporting) reporting.

I have used TestNG's IExecutionListener so that our reporting code gets invoked once TestNG is done with the execution for both the threads and json report files are generated. 
**Here is the code for Masterthought reporting.**

```java
package com.cucumber.testng.examples;

import net.masterthought.cucumber.ReportBuilder;
import java.io.File;
import java.util.ArrayList;
import java.util.List;

/**
 * Created by amit.rawat on 21/12/15.
 */
public class GenerateReport {
    public static void GenerateMasterthoughtReport(){
        try{
            String RootDir = System.getProperty("user.dir");
            File reportOutputDirectory = new File("target/Masterthought");
            List<String> list = new ArrayList<String>();
            list.add("target/cucumber1.json");
            list.add("target/cucumber2.json");

            String pluginUrlPath = "";
            String buildNumber = "1";
            String buildProject = "cucumber-jvm";
            boolean skippedFails = true;
            boolean pendingFails = true;
            boolean undefinedFails = true;
            boolean missingFails = true;
            boolean flashCharts = true;
            boolean runWithJenkins = false;
            boolean highCharts = false;
            boolean parallelTesting = true;
            boolean artifactsEnabled = false;
            String artifactConfig = "";

            ReportBuilder reportBuilder = new ReportBuilder(list, reportOutputDirectory, pluginUrlPath, buildNumber,
                    buildProject, skippedFails, pendingFails, undefinedFails, missingFails, flashCharts, runWithJenkins,
                    highCharts, parallelTesting);

            reportBuilder.generateReports();
        }catch(Exception e){
            e.printStackTrace();
        }
    }
}
```

# Outro  #
So to tie up all the pieces which we discussed in this blog, [here](https://github.com/sahajamit/cucumber-jvm-testng-integration/blob/master/testng.xml) is the complete source code on Github which can be referred. In this blog we have seen how to run the cucumber feature files in parallel using testng.



