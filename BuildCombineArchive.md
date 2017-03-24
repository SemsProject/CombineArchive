Build CombineArchive 
======================

When you've cloned the source code:

```sh
git clone git@github.com:SemsProject/CombineArchive.git
```

There are two supported options to build this project:

* [Build with Maven](#build-with-maven)
* [Build with Ant](#build-with-ant)

Build with Maven 
-----------------

[Maven](https://maven.apache.org/) is a build automation tool. We ship a [source:pom.xml pom.xml] together with the sources which tells maven about versions and dependencies. Thus, maven is able to resolve everything on its own and, in order to create the library, all you need to call is `mvn package`:

```sh
usr@srv $ mvn package

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running de.unirostock.sems.cbarchive.TestArchive
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.314 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
```

That done, you'll find the binaries in the `target` directory.

Build with Ant 
---------------

[Ant](https://ant.apache.org/) is an Apache tool for automating software build processes. There is a [source:build.xml build.xml] file included in the source code that tells ant what to do. Since ant is not able to resolve the dependencies you need to create a directory `lib` containing the following libraries:

* [BFLog](http://bin.sems.uni-rostock.de/BFLog/)
* [json-simple](https://code.google.com/p/json-simple/)
* [JDOM2](http://jdom.org/)

We defined multiple targets in the `build.xml`. They can be displayed by calling `ant -p`:

```sh
usr@srv $ ant -p
Buildfile: /path/to/CombineArchive/build.xml

                Library to read/create/write/manipulate/... COMBINE archives

Main targets:

 clean    clean up
 compile  compile the source
 dist     generate the distribution
 init     initialize workspace
 sign     sign a dist
Default target: dist
```

* `clean up` will delete all compiled files and produced libraries
* `compile` compiles the source code
* `dist` bundles all compiled binaries into a jar library

For example, to create the jar library just run `ant dist`:

```sh
usr@srv $ ant dist
Buildfile: /path/to/CombineArchive/build.xml

init:

compile:
    [javac] Compiling 13 source files to /path/to/CombineArchive/build

dist:
      [jar] Building jar: /path/to/CombineArchive-0.6.jar
      [jar] Building jar: /path/to/CombineArchive-0.6-fat.jar

BUILD SUCCESSFUL
```

