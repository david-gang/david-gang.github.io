---
layout: post
title: How to sync grunt build results with tomcat webapps folder
---
##Task
Plug in an angular application in an existing maven project which creates the webapps folder of the tomcat.

#Try 1 - filesync plugin

The plugin i planned to use was the <a href="http://marketplace.eclipse.org/content/filesync">filesync plugin</a>.

Meaning that my source folder www will be synced to webapps/x where x is my application.

This worked fine, but the problem was that i had uglified javascripts which were created by grunt tasks.

Because they were created externally, eclipse was not aware of their existence and therefore did not sync them.

#Try 2 - filesync plugin and "refresh using native hooks or polling"

The main problem was that eclipse was not aware of changes caused by external programs.

So the easiest solution at this time was to configure eclipse:

I checked the following option
Preferences -> Genneral -> Startup and SHutdown -> Workspaces -> refresh using native hooks or polling

Now everything worked fine because eclipse knew from the operating system that the grunt task changes the files, refreshed the workspace and then the filesync synced all the files.

Here the problem started.

Suddenly i got out of heap messages, when running thh maven build the java classes got corrupted ...

#Try3 - Use the grunt-sync plugin

Finally i removed the filesync plugin usage and used the grunt-sync <a href="https://www.npmjs.com/package/grunt-sync">plugin</a>.

```javascript

 sync: {
		      eclipse: {
		        files: [{
		          cwd: build_src,
		          src: [
		            '**', /* Include everything */
		          ],
		          dest: tomcat_dev_dir,
		        }],
		        updateAndDelete: true,
		        verbose: true // Display log messages when copying files
		      }
		    },

```

I had just to configure it that it will called in the initial build and everytime a file was changed through the grunt watch task and everything worked fine

## Summary

When using grunt try to stay as grunty as possible and don't try to take existing solutions from the JSP world for grunt tasks
