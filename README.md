# Pintos Dev Container

A VSCode dev container enables you to use VSCode GUI to debug Pintos with GDB.

![example](./images/pintos_debug.png)

## Get Started

* Make sure Docker, VSCode and [Remote Development Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack) are installed
* Press [Use this template](https://github.com/LiangLouise/pintos_dev_container/generate) to create your own repository and then clone it
* Choose `Reopen in Container` in VScode. For details refer this [tut](https://code.visualstudio.com/docs/remote/containers#_quick-start-open-an-existing-folder-in-a-container)
* Wait for container to be built
* After the new VSCode window pops out, then you are good to go

Note: If you are using Windows + WSL2, please ensure EOL is set to be `LF` for all text files, otherwise shell scripts won't be executed normally when docker building the image. This can be done by updating the config of git `core.autocrlf`. For details, please follow this [post](https://stackoverflow.com/a/13154031).

## Debug a Test with VSCode

* Insert breakpoints to the lines you want to debug
* Go to `Run and Debug` tab, select the configurations for the project you are working on, and press play button
* Wait for compiling and in the prompt box
  * for Project 1, type the test name, e.g., `alarm-multiple`, you want to debug
  * for Project 2, since we need load program and pass arguments to it: 
	* First type the program name, e.g., `args-muliplte`, for gdb to load user program symbols
	* Then type the program name for pintos file system to load (e.g., `args-multiple`.)
	* Finally type the task to run which include the user program name and arguments to pass (e.g., `args-multiple some arguments for you!`)

For later projects, please modify the arguments and working directory of the compile and run tasks in [tasks.json](./.vscode/tasks.json) to fit your need. And please also update the `program` entry in the [launch.json](.vscode/launch.json) to points to the `kernerl.o` you want to test.

For details about how to write your own `tasks.json` and `launch.json`, please refer to the docs of VSCode.

### Debug a Specific Test

Usually you may want to focus on some specific tests, then you could copy the pintos cmd from `make grade` and create a new task and launch job where input variables are replaced with binary file name and test name.

For example, in project 3, if you want to debug `page-parallel`:

<details>
	<summary><code>launch.json/configurations</code></summary>
	
```json

{
    "name": "[P3] Debug page-parallel",
    "type": "cppdbg",
    "request": "launch",
    "program": "${workspaceFolder}/src/vm/build/kernel.o",
    "preLaunchTask": "[P3] Run child-linear",
    "miDebuggerServerAddress": "localhost:1234",
    "stopAtEntry": false,
    "cwd": "${workspaceFolder}/src",
    "environment": [],
    "externalConsole": false,
    "MIMode": "gdb",
    "setupCommands": [
	{
	    "description": "Enable pretty-printing for gdb",
	    "text": "-enable-pretty-printing",
	    "ignoreFailures": true
	},
	{
	    "text": "source -v ${workspaceFolder}/src/misc/gdb-macros",
	    "description": "Import Pintos GDB macros file",
	    "ignoreFailures": false
	},
	{
	    "text": "loadusersymbols ${workspaceFolder}/src/vm/build/tests/vm/child-linear",
	    "description": "Import user program",
	    "ignoreFailures": false
	},
	{
	    "text": "loadusersymbols ${workspaceFolder}/src/vm/build/tests/vm/page-parallel",
	    "description": "Import user program",
	    "ignoreFailures": false
	}
    ],
    "symbolLoadInfo": {
	"loadAll": true,
	"exceptionList": ""
    }
}
```

</details>

and 

<details>
	<summary><code>tasks.json/tasks</code></summary>
	
```json

{
    "label": "[P3] compile",
    "type": "shell",
    "command": "make",
    "options": {
	"cwd": "${workspaceFolder}/src/vm"
    }
},
{
    "label": "[P3] Run child-linear",
    "type": "shell",
    "isBackground": true,
    "problemMatcher": [
	{
	    "pattern": [
		{
		    "regexp": ".",
		    "file": 1,
		    "location": 2,
		    "message": 3
		}
	    ],
	    "background": {
		"activeOnStart": true,
		"beginsPattern": ".",
		"endsPattern": ".",
	    }
	}
    ],
    "dependsOn": [
	"[P3] compile"
    ],
    "command": "pintos -v -k -T 60 --qemu --gdb --filesys-size=2 -p tests/vm/page-parallel -a page-parallel -p tests/vm/child-linear -a child-linear --swap-size=4 -- -q  -f run page-parallel",
    "options": {
	"cwd": "${workspaceFolder}/src/vm/build"
    }
}
```
	
</details>
	
### User-defined Macros

To use user-defined Macros in [E.5.1 Using GDB](https://thierrysans.me/CSCC69/projects/WWW/pintos_10.html#SEC152), you can open the debug console tab and type the command in the panel with prefix `-exec`. For example, to run command `dumplist`, type `-exec dumplist &all_list thread allelem` and press Enter.

## Working With Existing Pintos Repo

If you have already cloned the repo from [CSCC69-Pintos](https://github.com/ThierrySans/CSCC69-Pintos), to enable using devcontainer and debug in VSCode:

* Copy the entire directory [.vscode](./vscode) to the root of your repo;
* If you are using Dockerfile to build your own image, update the [dockerfile](https://github.com/LiangLouise/pintos_dev_container/blob/cee2d30a6bfacf4a94ab882adb1e828149b839aa/.devcontainer/devcontainer.json#L6) to the path that points to your own dockerfile;
* If you are using image `thierrysans/pintos` pulled from dockerhub, replace the content of devcontainer.json with
```json
{
    "name": "demo",
    "runArgs": [
        "--rm"
    ],
    "image": "thierrysans/pintos",
    "extensions": [
        "ms-vscode.cpptools"
    ],
    "workspaceMount": "source=${localWorkspaceFolder},target=/pintos,type=bind,consistency=cached",
    "workspaceFolder": "/pintos",
    // Set *default* container specific settings.json values on container create.
    "settings": {
        "terminal.integrated.shell.linux": "/bin/bash"
    },
}
```
* Choose `Reopen in Container` in VScode, then you are good to go.

Note that Clang-Format won't work if your dockerfile or image doesn't have `clang-format`.

## Formatting

The formatting style of Pintos as recommended in the [Coding Standards](https://thierrysans.me/CSCC69/projects/WWW/pintos_8.html#SEC138) is GNU style. Thus this dev container uses [clang-format](https://clang.llvm.org/docs/ClangFormat.html) to format the source code automatically via. a VSCode extension [Clang-Format](https://marketplace.visualstudio.com/items?itemName=xaver.clang-format).

You may override the settings in [settings.json](./.vscode/settings.json) to disable formatting on save or change the formatter. You may also change the formatting style in the config file [.clang-format](./.clang-format) as your preference.

## References

* [Debug Pintos using GDB](https://thierrysans.me/CSCC69/projects/WWW/pintos_10.html#SEC151)

* [VSCode C/C++ Debug](https://code.visualstudio.com/docs/cpp/cpp-debug)

* [Debug helloworld.cpp](https://code.visualstudio.com/docs/cpp/config-linux#_debug-helloworldcpp)
