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
  * for Project 1,  type the test name, e.g. `alarm-multiple`, you want to debug
  * for Project 2, you need to type the test name twice for gdb to load user program symbols and pintos to run the test
* Wait for the debug session to begin

Note that the current set up only works for Project 1. For later projects, please modify the arguments and working directory of the compile and run tasks in [tasks.json](./.vscode/tasks.json) to fit your need. And please also update the `program` entry in the [launch.json](.vscode/launch.json) to points to the `kernerl.o` you want to test.

For details about how to write your own `tasks.json` and `launch.json`, please refer to the docs of VSCode.

### User-defined Macros

To use user-defined Macros in [E.5.1 Using GDB](https://thierrysans.me/CSCC69/projects/WWW/pintos_10.html#SEC152), you can open the debug console tab and type the command in the panel with prefix `-exec`. For example, to run command `dumplist`, type `-exec dumplist &all_list thread allelem` and press Enter.

## Formatting

The formatting style of Pintos as recommended in the [Coding Standards](https://thierrysans.me/CSCC69/projects/WWW/pintos_8.html#SEC138) is GNU style. Thus this dev container uses [clang-format](https://clang.llvm.org/docs/ClangFormat.html) to format the source code automatically via. a VSCode extension [Clang-Format](https://marketplace.visualstudio.com/items?itemName=xaver.clang-format).

You may override the settings in [settings.json](./.vscode/settings.json) to disable formatting on save or change the formatter. You may also change the formatting style in the config file [.clang-format](./.clang-format) as your preference.

## Working With Existing Pintos Repo

If you have already cloned the repo from [CSCC69-Pintos](https://github.com/ThierrySans/CSCC69-Pintos), to enable using devcontainer and debug in VSCode:

* Copy the entire directory [.vscode](./vscode) to the root of your repo;
* If you are using Dockerfile to build your own image, update the [dockerfile](https://github.com/LiangLouise/pintos_dev_container/blob/cee2d30a6bfacf4a94ab882adb1e828149b839aa/.devcontainer/devcontainer.json#L6) to the path that points to your own dockerfile;
* If you are using image of `thierrysans/pintos` pulled from dockerhub, replace the content of devcontainer.json with
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

## References

* [Debug Pintos using GDB](https://thierrysans.me/CSCC69/projects/WWW/pintos_10.html#SEC151)

* [VSCode C/C++ Debug](https://code.visualstudio.com/docs/cpp/cpp-debug)

* [Debug helloworld.cpp](https://code.visualstudio.com/docs/cpp/config-linux#_debug-helloworldcpp)
