<properties pageTitle="Debugging unit tests"
  description="Debugging unit tests"
  services=""
  documentationCenter=""
  authors="Kraig Brockschmidt" />

#Debugging unit tests
One of the most significant benefits to doing your unit testing inside Visual Studio is the ability to debug your code during a test, and to debug the tests themselves. After all, unit tests are code, and so that code will also have bugs.

The unexpected test failure in the previous section is not the result of buggy unit code, but actually a buggy unit test. Let’s then use the debugger to find out why.

First, note that when you have any of your JavaScript files open in the Visual Studio editor, you can set breakpoints on any line of code, such as the beginning of the truncation test:

![Setting a breakpoint in a unit test](media/debug/01-debug-breakpoint.png)
 
However, when you run tests through Test Explorer, those breakpoints are ignored because a test runner like Chutzpah is spawning separate PhantomJS processes to load and execute all the JavaScript, which bypasses the Visual Studio IDE entirely.

Fortunately, you can tell Visual Studio to hook itself into the execution process through the **Test > Debug** menu, where you’ll find options for **Selected Tests** and **All Tests**. You can also right-click a failed test and select **Debug Selected Tests**:

![Selecting a test to debug](media/debug/02-debug-selected.png)

With any of these commands, Visual Studio instructs the test runner to execute the code in the browser, which will appear during a debugging session along with a Diagnostics Tool window. Visual Studio attaches its debugger to that browser so that your breakpoints will pause execution, wherever those breakpoints are set. At that point you’ll enjoy all of Visual Studio’s debugging features. Reports from the test framework will also appear in the browser.

After hitting the first breakpoint in the truncation test, we can right-click on the```var norm``` line, select **Run to cursor**, and then step over or through the call to ```normalizeData```. Coming back from that and hovering over the ```name``` variable, we can see that it contains exactly what we’d expect, including a truncated ```name``` property:
 
![Idenfitying the bug in the debugger](media/debug/03-debug-identify-bug.png)

Ah, now we see the problem! Our unit test dereferences ```norm.Name``` instead of ```norm.name``` (lowercase n on name), which is why the failure report said that the Actual value—the first argument to the ```equal``` check—was undefined. It’s a simple mistake, but one that could be confusing if we didn’t carefully examine the failure report and check the code in the debugger.

Stopping the debugger, changing the two instances of ```norm.Name``` to ```norm.name```, saving ```normalize_tests.js```, and rerunning the test, we find that it now passes.

>**Note**: although the **Run All** command for tests automatically saves changed files, rerunning a selected test does not. If you forget to save a file before running a selected test, you could be quite confused as to why it’s still failing!

##Runtime variances

Having fixed the bugs in the truncation test, only one failed test remains: “defaults with unknown fields, similar properties.” What’s confusing here is that the test itself is almost identical to the test before it:

```javascript
test('defaults with unknown fields, differing by case', function () {
    var json = '{"name": "Maria", "personalIdentifier": 2111858}';
    var norm = normalizeData(json);
    equal(norm.name, "default"); //Default
    equal(norm.id, 0); //Default
});

test('defaults with unknown fields, similar properties', function () {
    var json = '{"nm": "Maria", "pid": 02111858}';
    var norm = normalizeData(json);
    equal(norm.name, "default"); //Default
    equal(norm.id, 0); //Default
});
```

Something strange is happening, so we go into the debugger with this test. But lo and behold!—the test passes! This is also indicated in the browser during the debugging session:

![Browser indicating that tests pass in the debugger](media/debug/04-debug-browser-report.png)
 
What, exactly, is going on? Why is the original (non-debug) failure report talking about nulls?

![Odd failure report about a null object](media/debug/05-debug-odd-failure.png)

The answer is that the runtime used for unit testing will likely be different than the one used by the mobile platform to run the final app. The same applies during debugging. In this case, running unit tests outside the debugger uses PhantomJS, whereas the browser (Internet Explorer in this case) is used when debugging. 

Turns out the two runtimes differ in their implementation of ```JSON.parse``` where a leading zero on an integer value is concerned:

```javascript
    var json = '{"nm": "Maria", "pid": 02111858}';
```

Internet Explorer’s implementation of ```JSON.parse``` is OK with this, and treats the value as an integer. The implementation in PhantomJS, on the other hand, throws an exception, which is why ```normalizeData``` returned ```null```. When we remove that leading zero in the string, the test passes in both environments.

```javascript
    var json = '{"nm": "Maria", "pid": 2111858}';
```

If you run into curious situations like this, then congratulations! You’ve likely discovered a small variation between runtimes! In such cases—which should be rare—you’ll need to take a good look at any subtle issues in your code, of course, and a good strategy is to write a couple more nearly identical test with more slight variations on input values. This will help you spot the exact cause of the divergent behaviors.

