<properties pageTitle="Use TypeScript in a Cordova project"
  description="This is an article on bower tutorial"
  services=""
  documentationCenter=""
  authors="jmatthiesen" />

# Get started with TypeScript modules
[TypeScript](http://www.typescriptlang.org) is a programming language that is a superset of JavaScript - offering classes, modules, and interfaces. You can use these features while developing your Cordova app and TypeScript will compile into simple JavaScript that will be deployed as part of your app.

When you begin to work on a Cordova app with TypeScript, one of the first decisions to make is how to structure your app. For most apps, you will want to use external modules. TypeScript also supports *internal* modules (now called namespaces), but now that many different tools provide TypeScript support, internal modules are becoming obsolete.

When you use external modules, you also need to use a module loader. CommonJs and AMD are two of the main specifications for module loaders. CommonJs is EcmaScript 6 compliant. In a Cordova app that uses CommonJs, you also need a bundling tool like Browserify or Webpack. Browserify and Webpack make it possible to use CommonJs in a client-side browser-based scenario (like Cordova). We will provide you with an example using Browserify.

With AMD, you can use RequireJS as your module loader. It provides asynchronous loading of modules, which can be helpful for some apps. It also allows you to use the .tsconfig file for your compiler without configuring a task runner like Gulp.

##<a name="samples"></a>Get the samples!

The starter samples extend the [Greeter tutorial](http://www.typescriptlang.org/Tutorial) from the TypeScript handbook and include some basic plugin code that supports Geolocation. The two samples are complete Visual Studio projects.

  * [CommonJs and Browserify sample](https://github.com/Mikejo5000/TS-CommonJs)
  * [AMD and RequireJS sample](https://github.com/Mikejo5000/TS-AMD)

##<a name="modules"></a>Create your modules!

Whether you use CommonJs or AMD, you can organize your modules in the same way. To organize the modules, each module goes into its own TypeScript (.ts) file. In both CommonJs and AMD, use the `export=` statement to expose the class or interface to the rest of the app. The following code shows the Student module.

    ```
    class Student {
        fullname: string;
        constructor(public firstname: string, public middleinitial: string, public lastname: string) {
            this.fullname = firstname + " " + middleinitial + " " + lastname;
        }
    }

    export = Student;
    ```
The Student module typically resides in the project's Student.ts file.

For any module that needs to use another module, use the `import` keyword include the following code at the beginning of the file.
    ```
    import Student = require('./Student');
    ```

Now, in the file that imports the module, you can call your module code.

    ```
    function createUser(loc: any) {
        var lastName = "User " + current++;
        let user = new Student("Jane", "M.", lastName, loc);
        showGreeter(user);
    }
    ```

##<a name="commonjs"></a>Set up your module loader using CommonJs and Browserify

When using Browserify, you can make APIs calls with Gulp instead of running Browserify in the command line. The output will be a single combined (bundled) JavaScript file and a single combined sourceMap file. The sourceMap file will map output back to your original .ts file and make it possible for you to debug your source files (the .ts files) when you run the app. As an alternative to Browserify, you can use Webpack in your app but using Gulp with Webpack is generally a little more complicated to set up.

Using Gulp, you also need to choose a method of compiling TypeScript in the Gulp file. Two common methods are [gulp-TypeScript](https://www.npmjs.com/package/gulp-typescript) and [tsify](https://www.npmjs.com/package/tsify), both npm packages. We are using tsify, which is often easier to use with Browserify and will also automatically call your .tsconfig file (see tsify docs for more specific behavior). Here is the main task in the Gulp file.

    ```
    gulp.task('default', function () {
         // set up the browserify instance on a task basis
        var b = browserify({
            entries: './scripts/index.ts',
            extensions: ['.ts'],
            debug: true
        });

        return b.plugin(tsify, { noImplicitAny: true }).bundle()
          .pipe(source('app.js'))
          .pipe(buffer())
          .pipe(sourcemaps.init({ loadMaps: true }))
              // Add transformation tasks to the pipeline here.
          .pipe(sourcemaps.write('./', { includeContent:false, sourceRoot:'../../' }))
          .pipe(gulp.dest('./www/scripts/'));
    });
    ```

In the preceding code, you pass your project's entry file (index.ts) to Browserify and then use tsify to compile the TypeScript and generate sourceMaps. You will use [gulp-sourcemaps](https://www.npmjs.com/package/gulp-sourcemaps) to modify the sourcemaps after they are generated. The **sourceRoot** property points the sourceMaps back to your TypeScript files, which is required for debugging.

Before you can run your app (by pressing F5), You need to run the Gulp task. Instead of running Gulp from the command line, you might want to use the Visual Studio Task Runner. To do this, choose **View**, **Other Windows**, **Task Runner Explorer**. If your Gulp task is set up correctly, you can run it from the Task Runner by right-clicking the task and choosing **Run**.

![VS Task Runner](media/tutorial-typescript-modules/ts-vs-task-runner.png)

For more info, [try the sample](https://github.com/Mikejo5000/TS-CommonJs).

###Troubleshooting? Let's fix it

[Can't hit breakpoints in your .ts files](#breakpoints)

##<a name="amd"></a>Set up your module loader using AMD and RequireJs

When using AMD for your module loader, you will want to use RequireJS for your module loader. You can use Visual Studio to compile the TypeScript. One advantage of using AMD and RequireJS is that you don't need to configure a Gulp task but can use Visual Studio to compile the TypeScript. With AMD, you need to make sure that your settings in the .tsconfig are all correct. Here is the .tsconfig file in the sample app.

{
  "compilerOptions": {
    "target": "es5",
    "module": "amd",
    "noImplicitAny": false,
    "removeComments": false,
    "sourceMap": true,
    "inlineSources": true,
    "noEmitOnError": true,
    "outDir": "./www/scripts"
  }
}

You must specify **amd** as the module loader. By specifying **outDir**, you can create multiple output .js files and sourceMaps that correspond to the original input files (one .js file per .ts file). See the TypeScript [compiler options](https://github.com/Microsoft/TypeScript/wiki/Compiler-Options) for more info.

To use RequireJS, you will need to reference RequireJS in your main HTML file and specify your main JavaScript entry file in the **data-main** attribute value.

```
<script data-main="scripts/index.js"
        type="text/javascript"
        src="lib/requirejs/require.js"></script>
```
This tells RequireJS what file to load first.

For more info, [try the sample](https://github.com/Mikejo5000/TS-AMD).

##Troubleshooting

Here are a few issues you may see when working on your own app.

###<a name="breakpoints"></a>Can't hit breakpoints in your .ts files

Most likely, this is caused by a problem in your sourceMaps. When running your app, look for your .ts files under **Script Documents** in Solution Explorer. They should look similar to the illustration below. You can right-click on the .ts file and choose Properties to view the current path used by the sourceMaps. If you are using Gulp, check the Gulp sample in this article to make sure that sourceMap-related properties are set correctly. Make sure that properties like **extensions**, **loadMaps** and **sourceRoot** are using the specified values.

![SourceMaps](media/tutorial-typescript-modules/ts-vs-task-runner.png)
