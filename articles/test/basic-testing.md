<properties pageTitle="Basic unit testing with QUnit and Karma"
  description="Basic unit testing with QUnit and Karma"
  services=""
  documentationCenter=""
  authors="Kraig Brockschmidt" />

#Basic unit testing in action with QUnit and Karma

Let’s now follow a piece of code and an associated unit test through the process. For this exercise, create a project folder with two subfolders, ```js``` and ```test```, where we’ll save the files involved.

##The unit
First, the unit is a simple function to convert a piece of JSON with one set of properties into an object with different properties. Save this code to ```js/normalize.js```:

```javascript
/** @description Converts JSON data that may contain Name and PersonalIdentifier 
 *    properties to an object with the properties name (string) and id (positive 
 *    integer up to 9999999999.
 * @param {string} jsonIn The JSON data to normalize.
 * @return {object} An object with name (string) and id (integer) properties,
 *    defaulting to "default" and 0, or null if the JSON is null or invalid.
 */
function normalizeData(jsonIn) {
    data = JSON.parse(jsonIn);
    return {
        name: data.Name,
        id: data.PersonalIdentifier
    };
}
```

Understand that this code is strictly part of the app’s functionality: it has nothing whatsoever to do with our choice of test framework or test runner. It’s also intentionally faulty so that we can learn about writing tests as we challenge the assumptions it makes about the JSON input.

>Note: although it’s tempting to give an example unit that just involves simple math or a string operation, that sort of code usually comes from a library that is already thoroughly tested. You need only write unit tests for your own code, not library code.

##The unit test
Next, each **unit test** is a piece of code that validates the unit by:

1. Calling it with specific inputs, and,
2. Checking the output against the expected value.

A unit test must follow the conventions of the test framework you’re using. In this case, let’s use **QUnit** (originally part of jQuery). Save this code to ```test/normalize_tests.js```:

```javascript
// Name the testing module for QUnit
QUnit.module("normalizeData function");

// The "test" method called here is defined by the testing framework, in this case
// QUnit. The first argument is the name/description of the test that will appear 
// in test reports. The second argument is the function that executes the test, in
// this case calling the normalizeData function with a certain piece of test data.

test('accepts golden path data', function () {
    var json = '{"Name": "Maria", "PersonalIdentifier": 2111858}';
    var norm = normalizeData(json);
    equal(norm.name, "Maria");
    equal(norm.id, 2111858);
});
```

Notice how this individual unit test is **specific**: it calls the unit under test with *one* set of inputs and gives an *exact* name/description for the test with those inputs. This follows the best practice of isolating each unit test to an individual test case, creating a 1:1 mapping between the name of the test, as it appears in reports, and the exact test case (that is, the arguments used in the test). When the test runner reports a failure, then, you know exactly where to look in your code and can easily step through that one test in the debugger to isolate the failure.

Although it will seem tedious to keep every test isolated—which means you might have 20 or more unit tests for one code unit!—it saves you time in the end. If you attempt to combine a bunch of cases into a single unit test, and that test fails, you’ll not immediately know which specific case failed. You’d then have to step through *every* case in the debugger, and, finding that process to be too tedious and time-consuming, you’ll end up breaking out every test case into its own individual unit test anyway. It’s best, then, to just write specific unit tests from the get-go that each handle one and only one test case. As we’ll see later on, thinking through test cases and then turning those into actual unit tests need not take a long time, so don’t let the idea that writing a bunch of tests for one unit will be tedious and time-consuming.

##The test runner
With the unit and unit test in hand, we now need a test runner that knows how to execute QUnit tests and report results. For this first exercise, we’ll run tests on the command line using Karma, which you’ll need to install along with QUnit as follows:

1.	Make sure you have Node.js installed. This will already be the case if you’ve installed the Visual Studio Tools for Apache Cordova, otherwise visit [https://nodejs.org](https://nodejs.org).  
2.	Open a command prompt and navigate to the folder you created for this exercise.
3.	Run the following commands:
| Command | Purpose |
| ```npm install --save-dev qunitjs``` | Installs QUnit |
| ```npm install --save-dev karma``` | Installs Karma |
| ```npm install --save-dev karma-qunit karma-<browser>-launcher``` | Installs Karma dependencies; replace ```<browser>``` with whatever you have installed, e.g. ```chrome```, ```firefox```, ```ie```. |

4.	Create a configuration file for Karma using its built-in utility. On the command line, go to the folder for this exercise and run ```karma init```. This will ask you a series of questions:
	1.	For the framework, press Tab until you see ```QUnit```
	2.	For the files, enter ```js/**/*.js``` and ```test/**/*.js``` (matching the folders where we put our unit and unit test code, with ```**``` meaning “include all subfolders”)
	3.	For the browser, select whichever you have installed. Note that browser names in the configuration file are case-sensitive, for example ```Chrome```, ```IE```, or ```Firefox```, whereas in their related npm package names they're lower case.
	4.	Accept the defaults for everything else. 

##Running the tests from the command line
Now you can run the tests from the command line:

```
karma start --single-run
```

You’ll see the browser—a suitable JavaScript runtime—launch to run the tests. Karma’s output shows on the console, and should include a report like this at the end of the chain:

```Firefox 40.0.0 (Windows 10 0.0.0): Executed 1 of 1 SUCCESS (0.025 secs / 0.004 secs)```

At this point we have all the mechanics in place to run QUnit unit tests with the Karma test runner from the command line. The ```karma``` command above can be easily run from within a longer build process defined with task runners like Grunt and Gulp, including a build process for a Cordova app.

##Command-line test runners in Visual Studio
Visual Studio can integrate a command-line test runner like Karma using its Task Runner Explorer and a task runner such as Gulp. This will bring Karma’s output into the IDE. For a tutorial, see $[Test Apache Cordova apps with Karma and Jasmine](../tutorial-testing-cordova/Karma.md). 
