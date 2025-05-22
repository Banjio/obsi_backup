# Javascript

Thee are many possibilities for debugging:
## 1. Client Side directly in the Browser
* Open the .html file you want to debug in the browser (For example in vscode > right click on file and open in default browser).
* Open the dev console (F12 in Firefox)
* Go to the debugger Tab and search for the file you want to debug. If your html file interacts with the js code you can now set breakpoints and debug if needed
## 2. Using vscode 
* __Note__: if you want to use firefox you need to install an extension `ext install firefox-devtools.vscode-firefox-debug`
* Create the folder .vscode and a launch.json (If not already created) and add the following:
	* File Debugging
```
{

"version": "0.2.0",

"configurations": [

{

"name": "Launch index.html",

"type": "firefox",

"request": "launch",

"reAttach": true,

"file": "${workspaceFolder}/searchbar.html"

}

]

}
```
*   Attach to running server (In the example we use vscode\`s buildin live server running on port 5500)
```
{

"version": "0.2.0",

"configurations": [

{

"name": "Launch localhost",

"type": "firefox",

"request": "launch",

"reAttach": true,

"url": "http://localhost:5500/frontend/",

"pathMappings": [{

"url": "http://localhost:5500",

"path": "${workspaceFolder}"

}]

}

]

}
```