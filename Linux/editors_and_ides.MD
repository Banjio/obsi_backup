# Visual Studio Code

```sh
$ paru -Sy visual-studio-code-bin 
```

The binary packages are in my opinion the closest to visual studio code on windows. 

## Visual Studio Code

Depending on the version your using, the setting sync is no longer working (Apparently this is also the case in the newest version for code-bin). Then use the extension

```sh
> ext install Shan.code-settings-sync
```

### Solution for Binary packages

Install 

```sh
$ sudo pacman -Sy gnome-keyring libsecret libgnome-keyring   
```

Additionally xdg-utils may be neccessary which is not installed on all manjaro installations

# Configure for Python

https://code.visualstudio.com/docs/python/python-tutorial

## Select an Interpreter 

    > `(Ctrl+Shift+P)`, start typing the Python: Select Interpreter

Unfortunately the interpreter does not show up in the status bar (https://github.com/microsoft/vscode-python/issues/15961) but you can verify by opening a new terminal.  

### Pythonpath selection

To somewhat mimick the behavior of pycharm use this confuration

Add This configuration to your launch.json

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Aktuelle Datei",
            "type": "python",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal",
            "justMyCode": true,
            "env": {"PYTHONPATH": "${workspaceFolder}${pathSeparator}${env:PYTHONPATH}"}
        }
    ]
}
```

Unfortunately you canot use the play button but have to use the shortcute F5 or Shift+F5 as explained here https://stackoverflow.com/questions/53653083/how-to-correctly-set-pythonpath-for-visual-studio-code

# Linux Tips and Tricks

* Open new instance of vscode: `code -n`