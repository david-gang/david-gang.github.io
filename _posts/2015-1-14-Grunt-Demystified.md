---
layout: post
title: Grunt demystified
---


When working with grunt it looks sometimes like black magic how it works. 
After looking a bit into the codebase i understood finally how this tool works.

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

If the required object resolves to a function it is called.


