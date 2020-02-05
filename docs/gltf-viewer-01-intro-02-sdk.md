---
id: gltf-viewer-01-intro-02-sdk
title: Software Development Kit
---

I've created a starting project on Github. It contains a basic skeleton for the application, third party libraries and some tools for building the project and run the tests.

## Quick introduction to Git

Git is a software to handle versioning and collaboration on a repository. Most often it is used for source code, but it can be used to keep an history of modifications on any kind of text files.

Using such tool is almost mandatory for software development: it gives us security, history of changes and easy collaboration.

For this tutorial we will use Github, but other providers exist such as https://bitbucket.org/ and https://gitlab.com/ (this last one is open source, I'm planning on migrating most of my projects to it).

## Creating a Github account

If you don't have one already, create a Github account on the website https://github.com/ and log in to your account.

## Forking the repository

Fork the following repository: https://github.com/Celeborn2BeAlive/gltf-viewer-tutorial. It can be done with the "Fork" button, top-right on the page.

Forking means that you create a copy of the repository on your own account. Github keeps track of fork relationships between repository. 

With this copy, you will have the opportunity to do whatever modifications you want and push them online for backup or collaboration with other people.

Another advantage is that you will be able to easily merge my modifications if I need to change the code for whatever reason.

## Installing Git

If git is not already installed on your system, install it.

On Linux, use your system package manager (apt for Ubuntu, pacman for Archlinux, etc).

On Windows, you can install Git for Windows at this URL: https://git-scm.com/download/win. It will install Git on your system but also a bash terminal emulator that will allow you to use the same commands as for Linux.

## Setup the folder hierarchy

The folder hierarchy for the project at the end will be:
- PROJECT_ROOT
  - gltf-viewer-tutorial-git
  - build-gltf-viewer-tutorial
  - glTF-Sample-Models (optional, see below)
  - test-scenes (WIP)
  - test-references (WIP)
  - test-results (WIP)
  
You can replace PROJECT_ROOT with the name of your choice (gltf-viewer would be a good name I think, but sometimes you have to make your own choices).

The children folders will be:
- *gltf-viewer-tutorial-git* The code repository, which is a local clone of the fork you made on Github
- *build-gltf-viewer-tutorial* The build folder, it will contain the binaries compiled from the source code
- *glTF-Sample-Models* This one is optional. It should be a clone of the repository https://github.com/KhronosGroup/glTF-Sample-Models but only if you have enough disk space on your computer (2.5 GB approximately)

The following folders are Work in Progress, don't try to create them for now:
- *test-scenes* This folder will contains a subset of the scenes from glTF-Samples-Models, only to perform NRU tests.
- *test-references* This folder will contains reference images and logs for the scenes stored in test-scenes and glTF-Sample-Models, at each checkpoint of this tutorial.
- *test-results* This folder will contain results from tests. You don't need to create this one manually, the test scripts will do.

These folder names are quite important here because the test scripts I wrote depend on it. If you were creating this project from scratch all by yourself, you could use the names you want.

Start by creating the basic structure (replace PATH/TO/PROJECT_ROOT with a valid path and the name you want to give to the root folder):

```bash
mkdir -p PATH/TO/PROJECT_ROOT
cd PATH/TO/PROJECT_ROOT
```

## Cloning your fork and other repositories

We use the `git clone URL LOCATION` command to clone a repository. The URL can be a http link to a web repository or the path to a folder that is already a git repository. LOCATION is optional, if specified the repository is cloned in the corresponding folder. Otherwise it's cloned in a folder with the same name as the repositoru.

```bash
git clone URL_TO_YOUR_FORK gltf-viewer-tutorial-git
# Don't do these now, but come back later once its ready:
# git clone URL_TO_TEST_SCENES test-scenes
# git clone URL_TO_REF_IMAGES test-references
```

And if you have enough disk space:

```bash
git clone https://github.com/KhronosGroup/glTF-Sample-Models
```

If you don't have enough disk space for the whole repository, you can still get interesting models for your early tests here:
- [Sponza](/openglnoel/files/Sponza.zip) (50 MB once unzipped)
- [DamagedHelmet](/openglnoel/files/DamagedHelmet.zip) (12 MB once unzipped)

## How to commit and push your code

In the tutorial I will ask you to commit and push your code to Github. The commands to use for that are:
```bash
cd PATH/TO/PROJECT_ROOT/gltf-viewer-tutorial-git # We need to be in the source folder
git pull # Optional, to be sure you are up to date if you have other contributors
git add . # Add all modification
git commit -m "A message describing your changes" # Commit locally your modifications
git push # Push to the remote repository
```

## Building the project

At this point we should be able to build the project with CMake. CMake is a tool that is used to generate build files. This is useful for multi-platform projects to avoid dealing with different build system (Makefile, Visual Studio solutions, etc.).

A CMake project can easily be spotted if you see a CMakeLists.txt file at the root of the project. This is the file that should be written to tell CMake how to build the project (what files to compile, what executables/libraries to build, etc).

Most of C/C++ projects are now written with a CMakeLists.txt file. It has became the main build solution of the C++ ecosystem, so it is important to know how to use it.

### Install CMake

If you don't have CMake on your system, you need to install it.

On Linux, use your system package manager (apt for Ubuntu, pacman for Archlinux, etc).

On Windows, you can download the installer CMake at this URL: https://cmake.org/download/. During the installation procedure, check the box that asks if you want to put CMake in your PATH variable. You will need to restart your terminal for this change to take effect.

### Use CMake

When using CMake, we have 2 steps:
1. Configure the project with cmake - It generates the build solution (Makefiles for gcc, Visual Studio project for VS, etc) in a build folder
2. Compile the project with the native tool

Lets do that:

#### Configure the project

<span class="warning badge"></span> At university, if the build does not work, replace the last line by `cmake -DGLMLV_USE_BOOST_FILESYSTEM=ON ../gltf-viewer-tutorial-git`. Your gcc might be a bit too old.

```bash
cd PATH/TO/PROJECT_ROOT # Ensure that we are at the project root
mkdir build-gltf-viewer # Create the build folder
cd build-gltf-viewer # We need to be in the build folder before running CMake

cmake ../gltf-viewer-tutorial-git # We call cmake with the source folder as argument
```

You should see the CMake output telling you what it is doing. 

#### Compile the project

Once configuration is done we need to compile the project.

```bash
cmake --build . -j # We use "." to point the current working directory, which is the build directory. -j is to build with all cores.
```

The -j option is only supported on recent version of CMake.

If you read CMake tutorials online, they may ask you to use make instead:
```bash
make -j
```

This also work, but only on Linux (or Windows with some compilers, but not Visual Studio). Using `cmake --build` instead has several advantages:
- Works on all platform, with any build solution (gcc, clang, visual studio, etc.)
- Re-configure the build solution (first step) if CMakeLists.txt files have changed.
- Can be used to build a different build configuration for multi-configuration compilers (like Visual Studio)
- Can be used to build a different target (just an executable, or a library)
  
```bash
cmake --build . --target gltf-viewer --config Release -j # Only build target gltf-viewer in Release mode (more efficient)
cmake --build . --target toto --config Debug -j # Only build target toto in Debug mode (less efficient, but allows to debug more easily. it is the default config when you don't specify anything)
cmake --build . --target install # Only build the 'install' target. It is a special target that install the compiled files at a specific location, but it must be correctly defined in the CMakeLists.txt
```

For now you don't need to bother with all these commands, only use `cmake --build . -j` and things should work correctly. See below if you want to know more about CMake.

#### Changing the build configuration

Depending on your needs, you might want to build your project in Debug or Release mode. The Debug mode add information for debuggers such as gdb or Visual Studio debugguer, but it is slower. The Release mode is faster but hard to debug because some parts of the code may have been optimized. There is also the RelWithDebInfo mode, which is optimized but contains debugging information such as symbol names.

If your compiler supports multiple configurations, like Visual Studio, you don't need to re-run CMake, just run the build command by specifying the correct configuration. Example:

```bash
cmake --build . --target gltf-viewer --config Release -j
```

If your tool does not support multiple configuration out of the box, like Makefiles, you need to re-run CMake and specify a new configuration with this command line:

```bash
cmake -DCMAKE_BUILD_TYPE=Release . # In the build folder
cmake --build . -j # Then rebuild
```

Another alternative is to maintain multiple build directories, one for each configuration, when you often switch.

#### When to re-run CMake and how ?

It is somethimes necessary to re-run CMake, for example when you add new source files to your project.

In that case, run the following command in the build directory:
```bash
cmake .
```

You don't need to re-specify the path to the source folder since it is stored in the cache of the build folder.

## Run the executable

Now that it is compiled, you should be able to run:

```bash
./bin/gltf-viewer info
```

which should display some information about your OpenGL version.

On windows you might need to add Debug folder in the path (or Release if you build in Release config):

```bash
./bin/Debug/gltf-viewer info
```

## Base code

On order for you to quickly start, I have made a code template that must be completed during this tutorial.

The code is in PATH/TO/PROJECT_ROOT/gltf-viewer-tutorial-git/apps/gltf-viewer. The main file is already complete and just parse the arguments on the command line to forward them to the ViewerApplication class.

The ViewerApplication class has a constructor and a run() method. Most of the code should be implemented in the run() method, where I put comments of the form `// TODO ...`.

Just follow the tutorial and you will know when to replace what.

## Going further

### Git

You can find more information about Git here https://git-scm.com/ and thousands of tutorials online. I strongly advise you to learn how to do basic tasks on git from the command line such as:
- create a new repository
- cloning a repository
- attach a local repository to an online repository
- create branches, switch branches
- commit changes on a branch
- pushing and pulling changes
- merge branches
- use pull requests to send modifications from one repository to another

It is also possible to use graphical editors for Git, such as VS Code (with good extensions such as GitLens and Git Graph), Fork or Sublime Merge, but knowing how to do them on the command line is always good.

### CMake

CMake can be a bit hard to learn "correctly" because it has evolved a lot in a few years. There is the old way of doing things, and the new way. When you search for tutorial, try to search for "modern cmake" instead of just "cmake". In the meantime, you can use the following resources that are recent:
- Oh No! More Modern CMake - Deniz Bahadir - Meeting C++ 2019 https://www.youtube.com/watch?v=y9kSr5enrSk
- https://cliutils.gitlab.io/modern-cmake/ (Official book)

Note that the Meeting C++ conference seems to feature a new talk each year about CMake, so you have no excuse to be up to date (ok I admit I didn't saw the last one, so my CMake code may be crap, I'll fix it later ^^').

Also keep an eye on the CMake version you are using. If it is your system you should have installed the last one. Otherwise you need to check the version with `cmake --version` and only use CMake commands that are allowed for your version (or ask the system administrator to update the version).