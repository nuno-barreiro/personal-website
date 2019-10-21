---
Title: Begin cross platform desktop development with Electron and Typescript
Published: 2017-11-01 14:45:00 +0000
Tags: 
  - Electron
  - Typescript
---
If you would like to create a cross platform desktop application and already have the skills and experience on web development using HTML, JavaScript and CSS, then [Electron](https://electron.atom.io/) is probably what you are looking for.

Formerly known as Atom Shell and currently being developed by GitHub, this framework allows you to build desktop GUI application by leveraging some well-known technologies such as the Node.js run-time and Chromium.

Some well known examples of applications that were developed using Electron include the [Discord client](https://discordapp.com/), [GitHub desktop application](https://desktop.github.com/), and also the [Atom](https://atom.io/) and [VS Code](https://code.visualstudio.com/) editors, amongst many others.
Please follow the links to know more but right now, let’s start with our very own first Electron application, but instead of using plain vanilla JavaScript, let’s make leverage of another great language which is [Typescript](http://www.typescriptlang.org/).

First thing to do will be to create a directory for your app and inside of it run the below commands in order to initialise npm and install electron and typescript packages. Additionally we can also install [electron-reload](https://www.npmjs.com/package/electron-reload) which is a very useful package that allow us to see any changes done to our code on a running application without having to restart it.

``` bash
npm init -y 
npm install electron --save 
npm install typescript electron-reload --save-dev 
```

Now that the base setup is done we need to create the entry point for our application, configure the Typescript compiler and perform some additional changes to our package.json file in order to make our life easier whenever we need to run the application.

That being said, lets start by the creating a src directory under the root - not necessary but I just like to keep things organised this way - and the following two files inside of it:

*main.html* - this one will contain the markup for our main page. Its just a plain HTML, similar to those that you would create for any website.

``` html
<!DOCTYPE html>
<html>
    <head></head>
    <body>
        <h1>Hello Electron World!</h1>
    </body>
</html>
```

*main.ts* - which will contain the “entry point” code for our application. I think the code below speaks for itself. All we are doing for now is to open our main HTML page in a new window and setting some values for the size and position of it. We will also need to initialise the electron-reload package to listen to any changes to our code.

``` javascript
import { app, BrowserWindow } from "electron"; 
import * as electronReload from "electron-reload";

app.on("ready", () => {
    let mainWindow: BrowserWindow;

    mainWindow = new BrowserWindow({
        width: 1024,
        height: 768,
        center: true
    });

    // __dirname points to the "root" directory of the application
    // in this case it's the absolute path to "src"
    mainWindow.webContents.loadURL(`file://${ __dirname }/main.html`);
});

electronReload(__dirname); 
```

One thing to notice is the way we load the page URL using the *file://* protocol and the *__dirname* variable. We have to bear in mind that this is a desktop, not a web, application despite the fact we are using web technologies. This means that we are loading files located on our disk and not web addresses so we need a way to open those up and to know where they are located.

That’s exactly what we are doing here. The *file://* protocol allow us to load a file stored on our device and *__dirname* points to the root executing directory of our application, which in this case is the src folder.

Now that we have our entry point files created we need to configure typescript by adding a *tsconfig.json* file in the root directory and copy the following:

``` json
{ 
    "compilerOptions": { 
        "rootDir": "./src", 
        "module": "commonjs", 
        "target": "es5" 
     } 
}
```

Finally update the *package.json* file to properly configure the entry point of the application and to add a new start script that will trigger the Typescript compiler and run our electron application afterwards.

``` json
{ 
    "name": "electron-tsc-app", 
    "version": "1.0.0", 
    "description": "", 
    "main": "src/main.js", 
    "scripts": { 
        "start": "tsc && electron ." 
    }, 
    "keywords": [], 
    "author": "", "license": "ISC", 
    "dependencies": { 
        "electron": "^1.7.9" 
    }, 
    "devDependencies": { 
        "electron-reload": "^1.2.2", 
        "typescript": "^2.5.3" 
    } 
} 
```

We are now ready to start our app. All we have to do is to execute npm start from the command line and:

 ![Electron demo initial screen](/images/electron-tsc-app-initial-screen.png)

Our app is now running and since we included electron-reload in our application we’ll also be able to change the code and see those changes reflected in our application without having to restart it.

Nice, right? This is just the beginning. See you on a next post. 