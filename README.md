# Setting Up Electron + Vite + TypeScript
This is a tutorial for how to do a React + Vite + Electron +  TypeScript setup

**Note:** *I'm a complete beginner at this, and this is for me also, so i can check this (there could be mistakes)*

*Credit to:*  [Youtube](https://www.youtube.com/watch?v=fP-371MN0Ck)




## Setting Up Vite

* If you doesnt have installed Node.js
    * Link to official site [NodeJs](https://nodejs.org/en/download/).

* Go into the directory you want the app


> First Command
>> $ npm create vite . 

* we do now the React, so choose that <br/>
![Choosing React](/pics/create_vite.png)

* Choose TypeScript <br/>
![Choosing TypeScript](/pics/choosing_typescript.png)

* Choose no, so we do instead <br/>
![Choose no](/pics/choose_no.png)

And with that we are ready with the first step

> Second Command
>> $ npm i

This will just download usefull packages

### Checking if its working

>Third Command
>> $ npm run dev

* You should see something like this, and you can open the server by Ctrl+CLick on it <br/>
![Running Vite Server](/pics/running_vite_server.png)

And with that we set up our vite server

---

## Setting Up Electron



> Third Command
>> $ npm run build

* Inside the src directory create an other named **ui**
    * place all of the items inside which were in the src <br/>
    * ![Making the UI directory](/pics/src_ui.png)

* But with tha we also need to make change to the index.html
    * we have to change src from src/main.tsx to the following<br/>
    ```xml
         <script type="module" src="/src/ui/main.tsx"></script>
    ```

* The tutorial from wich i watch this somewhy deleting the public folder. 
    * So we have to delete the lines where its using it in the App.tsx

* When we run the following command *$ npm run build* it makes a directory named dist, which contains the ready code. I think this would work on any browser fine. But we want an app, and whe have to configure the vite.config.ts, becouse the electron build makes the same dist directory, which would cause conflicts.
```ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vite.dev/config/
export default defineConfig({
  plugins: [react()],
  build: {
    outDir: "dist-react",
  }
})

```

* If we run the command *$ npm run build* this will make a seperate folder named dist-react
* in the .gitignore put the following line 
```
dist-react
```
    * if you aren't familiar with this, this only tells the git to ignore this directory when you commit

>Fourth Command
>> $ npm install --save-dev electron

Inside the src folder make a folder named electron, and inside a main.js
```js
import {app, BrowserWindow} from 'electron';
import path from 'path';


app.on('ready', () => {
    const mainWindow = new BrowserWindow({});
    mainWindow.loadFile(path.join(app.getAppPath(), "dist-react/index.html"));
})
```

* Adding to package.json
```json
{
  "main": "src/electron/main.js",
}
```

* Inside the script in package.json add this two line
```json
{
"scripts": {
    "dev:react": "vite",
    "dev:electron": "electron .",
    }
}
```

* Now if you run *$npm run dev:electron* it will make a blank window
* To prevent this inside the vite.config.ts add the following line after the *plugin*
```ts
export default defineConfig({
  plugins: [react()],
  base: './',
  build: {
    outDir: "dist-react",
  }
})
```

* Now you should get a good electron interface, when you run *$npm run dev:electron*


And with that we are ready with electron
---

# Setting Up typeScript


* The project wich i was following doesn't use frontend so we have to congigure how we compile the code

* Inside the tsconfig.sjon is this. This seperates *maybe* the frontend and the backend. So we have to go inside the *tsconfig.app.json*
```json
{
  "files": [],
  "references": [
    { "path": "./tsconfig.app.json" },
    { "path": "./tsconfig.node.json" }
  ]
}
```
* Inside *tsconfig.app.json*
    * At the end add this line
    * *Note: i only use brackets so the markdown highligths this as a valid json code*
```json
{
    "exclude": ["src/electron"],
}
```

Now navigate inside the electron folder and make a file named tsconfig.json with the following content
```json
{
  "compilerOptions": {
    "strict": true,
    "target": "ESNext",
    "module": "NodeNext",
    "outDir": "../../dist-electron",
    "skipLibCheck": true,
  }
}
```

And now we can use typescript, so lets change our *main.js* file to *main.ts* and add this line
```ts
type test = string;
``` 

Inside the package.json we have to add this line
```json
{"scripts": {
    "transpile:electron": "tsc --project src/electron/tsconfig.json",
    }
}
```
>Fifth Command
>> $ npm run transpile:electron

This created the dist-electron folder which contains a file named main.js, So inside the package.json we modifie this line
```json
{
    "main": "dist-electron/main.js",
}
```

So if we run the command *$ npm run dev:electron* it should work just fine

Inside the .gitignore add this line
```
dist-electron
```


# Building the app

> Sixth Command
>> $ npm run --save-dev electron-builder

Make a file named electron-builder.json with the following content
```json
{
  "appId": "com.yourname.appname",
  "files": ["dist-electron", "dist-react"],
  "icon": "./desktopIcon.png",
  "mac": {
    "target": "dmg"
  },
  "linux": {
    "target": "AppImage",
    "category": "Utility"
  },
  "win": {
    "target": ["portable", "msi"]
  }
}
```

Inside the package.json add these lines
```json
{
"scripts": {
    "dist:mac": "npm run transpile:electron && npm run build && electron-builder --mac --arm64",
    "dist:win": "npm run transpile:electron && npm run build && electron-builder --win --x64",
    "dist:linux": "npm run transpile:electron && npm run build && electron-builder --linux --x64",
    }
}
```

> Seventh Command
>> $ npm run dist:(your-op)

And with that wqe should have creatied a desktop app

# Faster Development

> Eighth Command
>> $ npm run --save-dev cross-env

After running the command change the following line in package.json
```json
{
"dev:electron": "cross-env NODE_ENV=development electron .",
}
```

Inside the electron folder create a new file named util.ts


```ts
export function isDev(): boolean{
    return process.env.NODE_ENV === 'development';
}
```

Inside the vite.config.ts add the following lines
```ts
export default defineConfig({
  plugins: [react()],
  base: "./",
  build: {
    outDir: "dist-react",
  },
  server: {
    port: 5123,
    strictPort: true,
  },
})
```

* So if you run now *$ npm run dev:react* the port will be "http://localhost:5123/
    * You can choose any port to your liking




And modify the main.ts to the following

```ts
import {app, BrowserWindow} from 'electron';
import path from 'path';
import {isDev} from './util.js';


type test = string;


app.on('ready', () => {
    const mainWindow = new BrowserWindow({});
    if (isDev()){
        mainWindow.loadURL("http://localhost:5123/");
    }else{
        mainWindow.loadFile(path.join(app.getAppPath(), "dist-react/index.html"));
    }
})
```

* now run *$ npm run dev:react* so the server starts, and run *$ npm run dev:electron*. And if everything checks out, if you modify the App.tsx it should appear in the electron

But we can make it even better

> Nineth Command
>> $ npm i --save-dev npm-run-all

This will allow us to run the server and the electron with one command. After its installed modify the package.json to the following
```json
{
    "dev": "npm-run-all --parallel dev:react dev:electron",
    "dev:electron": "npm run transpile:electron && cross-env NODE_ENV=development electron .",

}
```

* And now if we run the *$ npm run dev* it should start the server and open electron
    * We can verify by modifying the App.tsx


---

Now everything is ready to make the best app ever.