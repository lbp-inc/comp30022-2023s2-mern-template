# Template for MERN Stack Development Environment[^1]
This is the template branch for Long Beach Place website development envinronment using MERN stack<br>

> [!NOTE]
> This documentation is written by Stephen Zhang<br>
> Please report any bug on Slack `@Stephen Zhang` or [contact by email](mailto://stephen.zhang1@student.unimelb.edu.au)

> [!IMPORTANT]
> **Prerequisites**<br>
> Node.js >= 18.16-LTS

## Repository Structure (Non-exhaustive)
```
. [Repository root]
└───webapp [Project root]
    ├───api [Back-end root]
    │   ├───routes
    │   │   └───helloworld.mjs [helloworld/ endpoint]
    │   ├───api.mjs [Main entry of back-end server]
    │   └───package.json [Node configuration, important]
    ├───public
    ├───src
    │   ├───App.js
    │   └───index.js
    └───package.json [Node configuration, important]
```

## Table of Content
- [Install Node.js](#install-nodejs)
- [To Create a New Project](#to-create-a-new-project2)
- [To Set up for an Existing Project](#to-set-up-for-an-existing-project)
- [Test the Front-end and Back-end Server at the Same Time](#test-the-front-end-and-back-end-server-at-the-same-time)
- [Troubleshoot](#troubleshoot)

## Install Node.js
**Linux**: <br>
Install package `nodejs`

**Windows**: <br>
Download from <a href="https://nodejs.org/en">official website</a><br>

**Linux user but on Windows for some reason**: <br>
```
winget install OpenJS.NodeJS.LTS
```
*(Only works on Windows Package Manager enabled devices)*

## To Create a New Project[^2]
### Create Front-end Application
Create a react project and go into the project directory
```
npx create-react-app [PROJECT_NAME]
cd [PROJECT_NAME]
```
### Test Front-end Application
Start Node.js server
```
npm start run
```
You should see something like
```
Compiled successfully!

You can now view [PROJECT_NAME] in the browser.

  Local:            http://localhost:3000
  On Your Network:  http://xxx.xxx.xxx.xxx:3000

Note that the development build is not optimized.
To create a production build, use npm run build.

webpack compiled successfully
```
At the same time, your default browser should launch and navigate to `http://localhost:3000/`, if not, you may manually launch a browser at your choice and navigate to [localhost:3000](http://localhost:3000/)<br>
You should be able to see a webpage with a spinning bluey React donut now

### Create Back-end Application
Create a separate folder
```
mkdir [BACK_END_FOLDER]
```
Initialise an empty Node.js project
```
cd [BACK_END_FOLDER]
npm init -y
```
Install back-end dependencies (configure for use of MongoDB as well)
```
npm install mongodb express cors dotenv
```
Populate the application with some code<br>
For example, to provide a simple HelloWorld return,<br>
Create a file as main entry of your back-end server<br>
```
// api.mjs

import express from "express";
import cors from "cors";
import helloworld from "./routes/helloworld.mjs";

const PORT = process.env.PORT || 3001;
const app = express();

app.use(cors());
app.use(express.json());

app.use("/helloworld", helloworld);

app.listen(PORT, () => {
  console.log(`Server is up on localhost:${PORT}`);
});
```
Create a folder to contains all the endpoints
```
mkdir routes
cd routes
```
Create an endpoint to return `HelloWorld`
```
// helloworld.mjs

import express from "express";

const router = express.Router();

router.get("/", async (req, res) => {
  let results = "HelloWorld";
  res.send(results).status(200);
});

export default router;
```

### Test Back-end Application
Start your back-end server
```
cd .. # Exit folder /routes and return to [BACK_END_FOLDER]/
node api.mjs # Start your main entry for back-end application
```
You should see
```
Server is up on localhost:3001
```
Open your browser, navigate to [localhost:3001/helloworld](http://localhost:3001/helloworld) and you should get a page containing only `HelloWorld`

### Connecting to Back-end Server from Front-end Application
Keep your back-end server up and running, open a new terminal and navigate to your front-end directory, namely `[PROJECT_NAME]/`<br>
Open `src/App.js` <br>
Import `React` and `Use and Effect` modules at the first line
```
import React, { useState, useEffect } from 'react';
```
In function `App()`, add the following hook binding before `return`
```
const [helloWorld, setHelloWorld] = useState(0);
useEffect(() => {
    async function getHelloWorld() {
        const response = await fetch("http://localhost:3001/helloworld/");   
        if (!response.ok) {
            window.alert(`Error: ${response.statusText}`);
            return;
        }
        setHelloWorld(await response.text());
    }
    getHelloWorld();
    return;
}, []);
```
And finally, find the tag `<header>`, replace everything inside the tag by
```
<h1>A Test React Application</h1>
<p>Message from back-end server: {helloWorld}</p>
```

The final result should look like this:
```
// App.css

import React, { useState, useEffect } from 'react';
import logo from './logo.svg';
import './App.css';

function App() {
  const [helloWorld, setHelloWorld] = useState(0);
  useEffect(() => {
    async function getHelloWorld() {
      const response = await fetch("http://localhost:3001/helloworld/");   
      if (!response.ok) {
        window.alert(`Error: ${response.statusText}`);
        return;
      }
      setHelloWorld(await response.text());
    }
    getHelloWorld();
    return;
  }, []);
  return (
    <div className="App">
      <header className="App-header">
        <h1>A Test React Application</h1>
        <p>Message from back-end server: {helloWorld}</p>
      </header>
    </div>
  );
}

export default App;
```

Finally, you may test your environment by following the instructions in [this section](#test-the-front-end-and-back-end-server-at-the-same-time)

## To Set Up for an Existing Project
### Install Node.js Dependencies
1. (For example) Clone this repository
2. Navigate into the project directory (not the root of the repository)
```
cd mern-template/webapp # Or equivalent
```
*(To check if you are in the right directory, type `ls` and check if `package.json` is present)*

3. Install React dependencies automatically
```
npm install
```
4. Install back-end dependencies automatically
```
cd api
npm install
```
That's it! You should be able to run the project<br>
Test the project by following the instructions in [this section](#test-the-front-end-and-back-end-server-at-the-same-time)

## Test the Front-end and Back-end Server at the Same Time
### Start the Back-end Server
> [!NOTE]
> Skip this part if you didn't close your back-end server

Under `[PROJECT_NAME]/[BACK_END_FOLDER]/` e.g. `mern-template/webapp/api`
```
node api.mjs
```
You should see
```
Server is up on localhost:3001
```

### Start the Front-end Server
Open a new terminal window, <br>
under `[PROJECT_NAME]/` e.g. `mern-template/webapp`
```
npm start run
```

On [localhost:3001/helloworld](http://localhost:3001/helloworld), you should see:
```
       A Test React Application
Message from back-end server: HelloWorld
```

That's it! You've successfully configured both front-end and back-end servers

## Troubleshoot
### `npm ERR! enoent ENOENT: no such file or directory, lstat '~\AppData\Roaming\npm'`
#### Description
The directory for installing Node packages cannot be found.
#### Solution
Create the directory manually by either using GUI or using command (may need elevated permission).

### `'react-scripts' is not recognized as an internal or external command`
#### Description
Your node modules are not installed correctly.
#### Solution
Please ensure you are in the project directory, not the root of the repository, and re-excute section `4. Install React dependencies automatically` from above.

### `Uncaught runtime errors`
```
ERROR
Failed to fetch
TypeError: Failed to fetch
    at getHelloWorld (http://localhost:3000/static/js/bundle.js:32:30)
    at http://localhost:3000/static/js/bundle.js:39:5
    at commitHookEffectListMount (http://localhost:3000/static/js/bundle.js:27262:30)
    at commitPassiveMountOnFiber (http://localhost:3000/static/js/bundle.js:28755:17)
    at commitPassiveMountEffects_complete (http://localhost:3000/static/js/bundle.js:28727:13)
    at commitPassiveMountEffects_begin (http://localhost:3000/static/js/bundle.js:28717:11)
    at commitPassiveMountEffects (http://localhost:3000/static/js/bundle.js:28707:7)
    at flushPassiveEffectsImpl (http://localhost:3000/static/js/bundle.js:30592:7)
    at flushPassiveEffects (http://localhost:3000/static/js/bundle.js:30544:18)
    at http://localhost:3000/static/js/bundle.js:30359:13
```
#### Description
Your back-end server is not up.
#### Solution
Follow the instruction [here](#start-the-back-end-server) to start up your back-end server.

[^1]: *Written by Stephen Zhang 28 Aug 2023 | Last Revised on 30 Aug*
[^2]: <a href=https://www.mongodb.com/languages/mern-stack-tutorial>MERN Stack Tutorial - MongoDB</a>
