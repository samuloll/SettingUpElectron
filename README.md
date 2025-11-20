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
    * ![Making the UI directory](/pics/src_ui)

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
* in the .gitignore put the following line *dist-react*
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





