
Creating this file under ```.vscode``` under the root folder and adding a file called ```launch.json``` with the content of:

```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [

        {
            "name": "Go: Launch File",
            "type": "go",
            "request": "launch",
            "mode": "debug",
            "program": "${file}",
            "cwd": "${fileDirname}",
        }
    ]
}
```