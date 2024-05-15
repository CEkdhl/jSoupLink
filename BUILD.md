# Building a jsoupLink paclet

For jsoupLink version 1.0.1. These notes by Felix Kasza <fk@mvps.org>.

Note that if you are happy with the original jsoupLink by Calle Ekdahl, you might as well skip all this and follow his "Installing jsoupLink" instructions in the `README.md` file which should be in the same directory as this file (`BUILD.md`).

Building your own paclet is something you might want to do if you wished to replace the underlying jsoup library with a newer version, for instance.

## Prerequisites

Mathematica v12.3 or later.

A git-cloned or downloaded copy of [felixkasza's fork](https://github.com/felixkasza/jsoupLink) of [Calle Ekdahl's](https://github.com/cekdahl/jSoupLink) jsoupLink. Your `jsoupLink` repository directory should contain `README.md`, `BUILD.md`, `jsoupLink.nb`, and a subdirectory named `jsoupLink`. We shall call the full path to this repository directory *`REPOPATH`*; for me, that might be something like `c:/code/repos/jsoupLink'.

In the REPOPATH, there is another jsoupLink directory; that is the root for the files that will go into the finished paclet. Make a note of the full path of this folder. On my system, it looks like this: `c:/code/repos/jsoupLink/jsoupLink`. We'll call this the *`SOURCEPATH`*. It should contain `PacletInfo.wl` and the subdirectories `assets`, `Java`, and `Kernel`. Additionally, a `build` subdirectory may be present: It is created by the paclet building process.

## Changing small things

If you wish to replace the jsoup library with a different version, do so in the `SOURCEPATH/Java' directory.

`SOURCEPATH/Kernel/jsoupLink.m` contains the Wolfram Language code that interfaces with the jsoup library. Mess with that at your own risk, with one exception: Should you choose to update the jsoup library, you have to change the following line in `SOURCEPATH/Kernel/jsoupLink.m`:

```mathematica
AddToClassPath[FileNameJoin[{DirectoryName[$InputFileName], "Java/jsoup-1.17.2.jar"}]];
```

This is line #21 as of this writing. As you can see, the versioned filename of the jsoup library is used here and needs to be changed if you build with a different jsoup version.

`SOURCEPATH/PacletInfo.wl` defines a `PacletObject[]` which contains an association of field names (all of them strings) to field values (strings or lists of strings or lists of rules) describing the contents of the paclet. If you made changes, be kind and update the version number; perhaps you can even see your way to mention the fork and your name in the `"Creator"`, `"Description"`, or `"URL"` fields.

The Tech Note [Paclets Overview](https://reference.wolfram.com/language/tutorial/Paclets.html) in the Wolfram documentation describes the contents of this file and much, much more.

## Building the jsoupLink paclet

Speaking of documentation, the Paclet Tools also have a [Tech Note](https://reference.wolfram.com/language/PacletTools/tutorial/CreatingPaclets.html) that ties the various commands used for building and deploying together. Let us get started:

Start a Mathematica session with a fresh kernel. Load the PacletTools package:

```mathematica
Needs["PacletTools`"]
```

Next, run

```mathematica
result = PacletBuild["SOURCEPATH"]
```

This should (assuming all is well) produce a `Success[]` object, like so:

![image of Success[] object](successobject.png)

You can query the result object:

```mathematica
In[4]:= result["Properties"]//TableForm

Out[4]//TableForm= PacletArchive
TotalTime
BuildPacletDirectory
PacletManifest
DocumentationBuildResults
```

## Loading the jsoupLink paclet from the build tree

At this point, there is no convenient single-file `jsoupLink-….paclet` yet, but you can already load the paclet directly from your file system (and remove it afterwards, e.g. to make more changes):

```mathematica
PacletDirectoryLoad[result["Location"]]
```

The paclet now is available to be found and loaded:

```mathematica
PacletFind["jsoupLink"]
```

The package name is the value of the `"Name"` field in the `SOURCEPATH/PacletInfo.wl` file, **not** the file name of the paclet. To make Mathematica forget about the package, use

```mathematica
PacletDirectoryUnload[result["Location"]]
```

## Creating the `jsoupLink-….paclet` file

Once the paclet works as you want it to, you can pack everything into a single file with

```mathematica
CreatePacletArchive["SOURCEPATH"]
```

The result is the full path to the created paclet file, e.g., `"REPOPATH/jsoupLink-1.0.1.paclet"`. Feel free to copy or move this file to wherever you like.

## Installing and uninstalling the jsoupLink paclet

*Installing* the paclet makes it known to the Mathematica system; from this moment on, its contents can be found by `PacletFind["jsoupLink"]`, ``Needs["jsoupLink`"]``, ``<<jsoupLink`‍``, &c.:

```mathematica
PacletInstall["REPOPATH/jsoupLink-1.0.1.paclet"]
```

Installing involves copying the paclet contents to Mathematica-provided directories and to update the list of available packages; the paclet file itself is no longer required afterwards.

To remove the paclet, use ``PacletUninstall["jsoupLink`"]`` and restart all running Mathematica kernels.
