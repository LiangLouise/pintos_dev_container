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
* Go to `Run and Debug` tab and press play button
* Type the test name, e.g., `alarm-multiple` for Project 1,  you want to debug
* Wait for the debug session to begin

Note that the current set up only works for Project 1. For later projects, please modify the arguments and working directory of the compile and run tasks in [tasks.json](./.vscode/tasks.json) to fit your need. And please also update the `program` entry in the [launch.json](.vscode/launch.json) to points to the `kernerl.o` you want to test.

For details about how to write your own `tasks.json` and `launch.json`, please refer to the docs of VSCode.

## Formatting

The formatting style of Pintos as recommended in the [Coding Standards](https://thierrysans.me/CSCC69/projects/WWW/pintos_8.html#SEC138) is GNU style. Thus this dev container uses [clang-format](https://clang.llvm.org/docs/ClangFormat.html) to format the source code automatically via. a VSCode extension [Clang-Format](https://marketplace.visualstudio.com/items?itemName=xaver.clang-format).

You may override the settings in [settings.json](./.vscode/settings.json) to disable formatting on save or change the formatter. You may also change the formatting style in the config file [.clang-format](./.clang-format) as your preference.

## References

* [Debug Pintos using GDB](https://thierrysans.me/CSCC69/projects/WWW/pintos_10.html#SEC151)

* [VSCode C/C++ Debug](https://code.visualstudio.com/docs/cpp/cpp-debug)
