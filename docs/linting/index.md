# Linting

###### Last updated: 27 August 2015

Linting is the process of performing static analysis on source code to flag patterns that might cause errors or other potential problems.

* [How do you start using the tool?](#howtostart)
* [How do you integrate the tool to be part of the toolchain?](#howtointegrate)
* [What are the best practices for using the tool as part of the toolchain?](#bestpractices)



<a name='howtostart'></a>
##How to start linting

While progressing through the development process, in addition to meeting functional requirements, it's also important to ensure that your code has no structural problems. Poorly structured code can impact the reliability and efficiency of your apps and make your code harder to maintain.  Linting is the key to finding and resolving these kinds of problems.  Linting tools can easily identify and correct common code mistakes, without having to execute your app or write any test cases.

Linters are available for most coding languages and can also typically be consumed several ways to suit your development needs.  IBM Open ToolChain offers integration with all of the following linters.  To get started, identify the linters that you would like to use from the information below.   These linting tools can be used independently or incorporated into your build process through the use of gulp files

* **JSHint** is a linting tool that flags suspicious usage in programs written in JavaScript. The core project consists of a library itself as well as a CLI program distributed as a Node module.
 * To learn more about installing and configuring JSHINT, see the [JSHINT documentation](http://jshint.com/docs/)

* **JSCS** is a code style linter for programmatically enforcing your style guide. You can configure JSCS for your project in detail using over 150 validation rules, including presets from popular style guides like jQuery, Airbnb, Google, and more.
 * To learn more about installing and configuring JSCS, see the [JSCS documentation](http://jscs.info/overview)

* **HTMLLint** is a linting tool for HTML5. It statically checks your documents for common errors so that you can identify problems in your code.
 * To learn more about installing and configuring HTMLLINT, see the [HTMLLINT documentation](https://github.com/htmllint/htmllint/wiki/htmllint-manual)

* **CSSLint** is a linting tool that points out problems with your CSS code. It does basic syntax checking as well as applying a set of rules to the code that look for problematic patterns or signs of inefficiency. The rules are all pluggable, so you can easily write your own or omit ones you don't want.
 * To learn more about installing and configuring CSSLint, see the [CSSLintdocumentation](https://github.com/CSSLint/csslint/wiki)



<a name='howtointegrate'></a>
##How to integrate linting

The linting tools described in this topic can all be integrated seamsly with your project using the IBM Open ToolChain.  To accomplish this integration, we'll use [gulp](http://gulpjs.com/), a JavaScript task runner, to set up and configure the linters, and will then run them as a build stage in the DevOps Pipeling.

###Linting plug-ins for gulp:
* [JSHINT plug-in for gulp](https://www.npmjs.com/package/gulp-jshint)
* [JSCS plug-in for gulp](https://www.npmjs.com/package/gulp-jscs)
* [HTMLLINT plug-in for gulp](https://www.npmjs.com/package/gulp-htmllint)
* [CSSLINT plug-in for gulp](https://www.npmjs.com/package/gulp-csslint)

###Steps for integration
1. Create a gulpfile.js file in the root of your project.  The following example can be used to get started.
```
  var gulp = require('gulp');
  
  gulp.task('default', function() {
    // place code for your default task here
  });
```
2. Next, use the following snippets to add one or more of the linting plug-ins to your gulpfile.

 **JSHINT**
 ```
  var jshint = require('gulp-jshint');
 
  gulp.task('lint-js', function() {
    return gulp.src(paths.js)
      .pipe(jshint())
      .pipe(jshint.reporter('YOUR_REPORTER_HERE'));
  });
 ```
 Notes:
 * `return gulp.src()` is used to defines the location of the files that should be linted
 * `.pipe(jshint())` defines a file named `jshint` used to default configurations
 * `.pipe(jshint.reporter('default'));` defines a reporting value.

 **JSCS**
 ```
  var jscs = require('gulp-jscs');
 
  gulp.task('lint-jscs', function () {
      return gulp.src(paths.js)
          .pipe(jscs());
  });
 ```
 **HTMLLINT**
 ```
  htmllint = require('gulp-htmllint');
 
  gulp.task('lint-html', function() {
      return gulp.src(paths.html)
          .pipe(htmllint());
  });
 ```
 **CSSLINT**
 ```
  var csslint = require('gulp-csslint');
 
  gulp.task('lint-csscss', function() {
    return gulp.src(paths.css)
      .pipe(csslint())
      .pipe(csslint.reporter());
  });
 ```

3. Next, we'll add a `paths` variable that we can use to define where the files are located that should be linted.
```
    paths = {
        html:['client/**/*.html','server/**/*.html'],
        css:['client/**/*.css','server/**/*.css'],
        js:['client/**/*.js','server/**/*.js','routes/**/*.js','!.meteor/**/*.js','app.js']
    },
```
    The variables used here should corespond with what is defined in step 2 for `return gulp.src(paths.*)`, and the actual paths referenced within the brackets should identify the location of your JavaScript, HTML, or CSS code.
    
Your resulting gulpfile should look something like this.
```
/**
* Linting Gulpfile
*/

var gulp = require('gulp'),
//These paths need to be changed based on the location of the respective files on your application
    paths = {
        html:['client/**/*.html','server/**/*.html'],
        css:['client/**/*.css','server/**/*.css'],
        js:['client/**/*.js','server/**/*.js','routes/**/*.js','!.meteor/**/*.js','app.js']
    },
    jshint = require('gulp-jshint'),
    csslint = require('gulp-csslint'),
    htmllint = require('gulp-htmllint'),
    jscs = require('gulp-jscs'),

/*
Gulp tasks for linting
*/

gulp.task('lint-js', function () {
    return gulp.src(paths.js)
    .pipe(jshint())
    .pipe(jshint.reporter('default'));
});

// Uses the .csslintrc file with some default configurations. Update the .csslintrc file based on the project requirements
gulp.task('lint-css', function () {
    return gulp.src(paths.css)
    .pipe(csslint('.csslintrc'))
    .pipe(csslint.reporter());
});

// Uses the .htmllintrc file with some default configurations. Update the .htmllintrc file based on the project requirements
gulp.task('lint-html', function () {
    return gulp.src(paths.html)
    .pipe(htmllint({
        htmllintrc: true
    }));
});

// Uses the .jscsrc file with some default configurations. Update the .jscsrc file based on the project requirements
gulp.task('lint-jscs', function () {
    return gulp.src(paths.js)
    .pipe(jscs());
});
```


<a name='bestpractices'></a>
##Linting Best Practices

What are the best practices for using the tool as part of the toolchain? For example with Slack what do we say (or point to) about using different channels, bringing automated events into channels to get notified of dev and ops events, etc.
