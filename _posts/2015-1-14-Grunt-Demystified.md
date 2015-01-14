---
layout: post
title: Grunt demystified
---

#Motivation
When working with grunt it looks sometimes like black magic how it works. 
This makes it harder to look for solutions when things go wrong.
The aim of this article is to explain what occurs behind the scenes when someone calls a grunt task from the command line

#Sample Gruntfile

Here is the sample gruntfile from the <a href="http://gruntjs.com/sample-gruntfile">grunt website</a>

```javascript
module.exports = function(grunt) {

  grunt.initConfig({
    jshint: {
      files: ['Gruntfile.js', 'src/**/*.js', 'test/**/*.js'],
      options: {
        globals: {
          jQuery: true
        }
      }
    },
    watch: {
      files: ['<%= jshint.files %>'],
      tasks: ['jshint']
    }
  });

  grunt.loadNpmTasks('grunt-contrib-jshint');
  grunt.loadNpmTasks('grunt-contrib-watch');

  grunt.registerTask('default', ['jshint']);

};
```

The key points here are the loadNpmTasks and initConfig functions.

#loadNpmTasks

Grunt requires the javascript files in the javascript files of the module argument.

If the required object resolves to a function it is called with the argument of the grunt object itself.

This is they key point of how grunt plugins are loaded.

#registerTask

This is an excerpt from grunt-jshint
```javascript
module.exports = function(grunt) {

  var path = require('path');
  var hooker = require('hooker');
  var jshint = require('./lib/jshint').init(grunt);

  grunt.registerMultiTask('jshint', 'Validate files with JSHint.', function() {
...
}
```javascript

This function is called by the line
> grunt.loadNpmTasks('grunt-contrib-jshint');

The function grunt.registerMultiTask registers the task in grunt by pushing the task in the grunt.tasks array.

When afterwards writing from the commandline 

> grunt jshint 

the jshint task is called.

The missing part is how we configure the tasks.

#initConfig

In the initConfig function the grunt.config object is initialized.
Every plugin can then read the relevant configuration through the grunt.config function.

