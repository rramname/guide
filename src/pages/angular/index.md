---
title: Starting with Angular with Express in Typescript in 10 mins
---
## Is it for me?
* Are you a newbie with "Express with typescript"?
* Are you also in love with typescript and want to do everything in Typescript?

Then please read on...

## What are we trying to achieve here?
By the end if this demo, you will have a development environment with only one directory for your angular and express code with typescript.
You will be able to use single command to start the server and serve the angular application.
And still be able to watch the changes separately.

## WHY?
We will have only one node_modules folder with all the required libraries.
You will have only one package.json file to maintain.
We won’t have to switch between editors or terminals for making and watching changes.

> Please note that this is to setup the development environment. Actual production deployment strategies may vary.
> Angular 5.2.0 and angular cli 1.7.4 was used for this article.

OK OK.. I get it. Please! Let’s see how it goes!!

*	Set up your angular application using angular cli. Basic instructions can be found at
 https://cli.angular.io/
*	For this application, we will not be using test framework but its always recommended to add tests for your project. Dry Run will first provide snapshot of the files and folder structure that cli will generate without actually creating any physical file. It’s always a better idea to run the command with –dry-run first.
  ```
  ng new AngularWithExpress –-skip-tests --dry-run
```
* If everything looks good and we go ahead and remove the -–dry-run flag from the command and execute.
 This will give us our angular application with its folder structure.
*	CD into the project so that we can get access to node_modules
  ```
  CD AngularWithExpress
```
* It would not be a bad idea to try and run the application to see if everything is good.
```
ng serve -o
```
This should open up your default browser at 4200 port.

* Awesome… let’s take a moment to appreciate the CLI.

* And.. we are back! Press Ctrl+C to kill the terminal.

* Now, we need to have our serverside code in node(Express) and; let’s not forget; Typescript
* Open the terminal window at the root and install express and it’s typings 
```
npm install express --save
```
* and Add typings 
```
npm install --save @types/express @types/es6-shim
```
* Please note that we don’t have to install typescript as its already install by the angular app created in first step.
* Let’s create a separate folder at root level (where src folder or angular app resides) for all of our Express related files and call it as MiddleTier.
* Add our main file for express app in this folder.
	  server.ts
* Add below code to start the server. This is just a basic code required by express.
 
 ```
 import * as express from 'express'
class app{
    express:express.Application;
    
    constructor(){
        
        this.express=express();
        this.express.listen(3000,()=>{
            console.log("express server started")
        })
    }
}
export default new app().express;

```
* OK. Now we have our express main file in typescript but we have to compile it in order to get it working.
* The next part is compiling and starting the server and then our angular application. And for this, we will be using gulp.

* We need below dev dependencies
 ```
 Npm install --save gulp gulp-nodemon  gulp-run gulp-typescript
 ```
* Add gulp file to the root
  Gulpfile.js

* Below is the code with explanation of gulp tasks
 	First, we ‘require’ the dependencies.
``` 
var gulp=require("gulp")
var ts=require("gulp-typescript");
var run=require("gulp-run")
var nodemon=require("gulp-nodemon")
```
* Add task to compile the typescript files under middle tier folder only and save the output under the folder called “server” at the root level.
```
gulp.task("compile",function(){
    gulp.src('MiddleTier/**/*.ts')
        .pipe(ts())
        .pipe(gulp.dest('./server/'));

})
```
* add a watch on the typescript file changes so that the compiler task can run automatically.
```
gulp.watch("MiddleTier/**/*.ts",["compile"])

```

* Once we have the compiled output, we need to start our server. Nodemon can do that and also add the watch for js files.
```
gulp.task('start', function () {
    nodemon({
      script: 'server/server.js'
    , env: { 'NODE_ENV': 'development' }
    })
})
```
* And the last task is to run the npm start script which will build and serve our angular app.
```
gulp.task('ui',function(){
      run('npm start').exec()
  })

```
* Now that we have the tasks, we have run them as well 
Let’s call the main task StartServer and under this main tasks, all the subtasks will run sequentially.
```
gulp.task('StartServer',['compile','start','ui'],function(cb){

  })
  ```
  
 * Edit package.json and change "start" script to
 ```
 "start": "ng serve -o"
 ```
  
* And that’s it. Open the terminal and run below command
	
 ```
 Gulp StartServer.
 
 ```

* It should compile the server.ts file, save it under ./server/ folder, start server at port 3000 and serve the angular application at port 4200.

* After this step, if you make a change in Angular App, only angular app will be refreshed and server will restart for each change related to express.

Happy Coding!!!


