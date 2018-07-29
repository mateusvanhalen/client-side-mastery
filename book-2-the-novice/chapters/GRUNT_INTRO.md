# Automating Tasks with Grunt

## What is Grunt?

Grunt is a command-line tool that you will be using throughout the course to automate repetitive tasks that you hate having to type over, and over, and over again in the terminal.

Visit the [Grunt website](https://gruntjs.com/getting-started) and follow the _Getting Started_ instructions to get it installed.

## Configuring Grunt in your Projects

### Grunt Directory to Contain Tasks

Go to any project directory that you have created so far in class, and use these command to get your directory structure set up, and create the initial files you will be using for automation.

```sh
touch Gruntfile.js
touch .eslintrc
npm init
```

When you run `npm init` you will see a prompt. Just keep pressing enter using all of the prompts are gone. This will create a `package.json` file in your directory.

```sh
npm i load-grunt-config grunt-contrib-watch grunt-eslint grunt grunt-http-server -D
```

```
mkdir grunt && cd grunt
touch aliases.yaml
touch eslint.js
touch http-server.js
touch watch.js
```

### List of Tasks

This file contains the name of all the automated tasks that you want to run when you type `grunt` in your project directory. You are going to start off with three tasks.

> grunt/aliases.yaml

```yaml
default:
  - "eslint"
  - "http-server"
  - "watch"
```

### Syntax Checking with Eslint

This file sets the rules that will be used to check your code. Normal language syntax rules get checked by default, and additional rules can be set in this file. For example, the `rules.semi` rule here is defaulted to 0, which will ignore whether you have semi-colons at the end of every expression or not.

* "off" or 0 - turn the rule off
* "warn" or 1 - turn the rule on as a warning (doesn’t affect exit code)
* "error" or 2 - turn the rule on as an error (exit code will be 1)

Go ahead and change that rule's value to 2 if you want to use semi-colons and have your code validated every time you save a file.

It is highly recommended that you bookmark the [listing of all Eslint rules](https://eslint.org/docs/rules/) so that as you gain more understanding of JavaScript, and want to have your code validated in more sophisticated ways in the future, you have the rule list handy.

> .eslintrc

```json
{
    "parserOptions": {
        "ecmaVersion": 8,
        "sourceType": "module",
        "ecmaFeatures": {
            "jsx": true
        }
    },
    "rules": {
        "semi": 0,
        "quotes": ["error", "double"],
        "eqeqeq": 2,
        "no-trailing-spaces": 2
    }
}
```

This next file is the Grunt configuration specifying which files should be checked for their syntax. This configuration specifies that every file in the `scripts` directory, or any sub-directory, that has a file extension of `.js` should be checked.

> grunt/eslint.js

```js
module.exports = {
    src: ["./scripts/**/*.js", "!node_modules/**/*.js"]
}
```

### Launching your Web Server

Rather than having one terminal window open for checking your JavaScript syntax, and another terminal window open for running `http-server`, you are going to combine them both. When you type in `grunt` in the terminal window, it will launch your web server **and** start checking your syntax.

This is the beginning of understanding the true power of a task runner like Grunt.

> grunt/http-server.js

```js
module.exports = {
    "dev": {

        // the server root directory
        root: "./",

        // the server port
        // can also be written as a function, e.g.
        // port: function() { return 8282; }
        port: 8080,

        // the host ip address
        // If specified to, for example, "127.0.0.1" the server will
        // only be available on that ip.
        // Specify "0.0.0.0" to be available everywhere
        host: "0.0.0.0",

        showDir : false,
        autoIndex: true,

        // server default file extension
        ext: "html",

        // run in parallel with other tasks
        runInBackground: true,

        // Change to true for grunt task to open the
        // browser automatically
        openBrowser : false
    }
}
```

### Keep Grunt Always Running

This next task will keep Grunt running indefinitely. Without the watch task, when you type the `grunt` command, all of the tasks will be performed and the process ends. The next time you want all the tasks to run again, you have to go to your terminal and type `grunt` again. This task is further automation, increasing your productivity as a developer. It just keep running until you intentionally terminate the process.

Once this task is configured, when you run `grunt`, it will continue to listen for changes to any JavaScript file, and then immediately check the syntax.

> grunt/watch.js

```js
module.exports = {
    scripts: {
        files: [
            "./scripts/**/*.js",
            "!node_modules/**/*.js"
        ],
        tasks: ["eslint"],
        options: {
            spawn: false,
            debounceDelay: 1000
        }
    }
}
```

## Loading All Tasks

Your `Gruntfile.js` only needs to require the `load-grunt-config` package, and it will read all of the tasks in the `grunt` directory.

> Gruntfile.js

```js
module.exports = function (grunt) {
    require("load-grunt-config")(grunt)
}
```

## Run Grunt

In your terminal, be in the directory that contains your `Gruntfiles.js` and then type in `grunt` and watch it start up. Come see a learning team member if it doesn't start correctly.
