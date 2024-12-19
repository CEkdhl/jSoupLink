# Building a jsoupLink paclet

For jsoupLink version 1.1.

Building your own paclet for jsoupLink is something you might want to do if you wished to replace the underlying jsoup library with a newer version, for instance. Usually, though, you will find it more convenient to grab the latest pre-built paclet from the [releases page](https://github.com/cekdahl/jSoupLink/releases); here is a link that goes straight to the [latest version](https://github.com/cekdahl/jSoupLink/releases/latest).

Once you have a paclet file in hand (say, jsoupLink-1.1.0.paclet), place it anywhere in your local file system, or just leave it in the download directory, and run `PacletInstall[path_to_paclet]` on it:

```mathematica
PacletInstall["/user/cekdahl/downloads/jsoupLink-1.1.0.paclet"]
```

This will copy the contents of the paclet into specific directories where Mathematica will look for them once you do a ``Needs["jsoupLink`"]``.

If you want to build your own paclet, read on for a description of the process. The accompanying `build-jsoupLink.nb` notebook does all these steps for you, except it goes straight to the finished paclet file, skipping the steps in section “Loading the jsoupLink paclet from the build tree”.


## Prerequisites

Mathematica v12.3 or later.

A git-cloned or downloaded copy of Calle Ekdahl’s [jsoupLink](https://github.com/cekdahl/jSoupLink). Your `jsoupLink` repository directory should contain `README.md`, `BUILD.md`, `jsoupLink.nb`, and a subdirectory named `jsoupLink`. We shall call the full path to this repository directory *`PROJECTPATH`*; for me, that might be something like `/code/repos/jsoupLink`.

In the PROJECTPATH, there is another jsoupLink directory; that is the root for the files that will go into the finished paclet. Make a note of the full path of this folder. On my system, it looks like this: `/code/repos/jsoupLink/jsoupLink`. We’ll call this the *`SOURCEPATH`*. It should contain `PacletInfo.wl` and the subdirectories `Documentation`, `Java`, and `Kernel`. Additionally, a `build` subdirectory may be present: It is created by the paclet building process and will be regenerated as required.

## Updating the jsoup library

If you wish to replace the jsoup library with a different version, place that version in the `SOURCEPATH/Java` directory, removing the older version that was already there. Building and packaging the paclet will include any files in this directory, and the code will look for its jsoup-classes among these files. (Usually, there is only one file: the most recent version of the jsoup Java class library.)

`SOURCEPATH/Kernel/jsoupLink.wl` contains the Wolfram Language code that interfaces with the jsoup library. Mess with that at your own risk.

`SOURCEPATH/PacletInfo.wl` defines a `PacletObject[]` which contains an association of field names (all of them strings) to field values (strings or lists of strings or lists of rules) describing the contents of the paclet. If you made changes, be kind and update the version number; perhaps you can even see your way to mention the fork and your name in the `"Creator"`, `"Description"`, or `"URL"` fields.
The file *MUST NOT* contain anything except the PacletObject and its data, not even a comment; the reason is the very restrictive parsing.

The Tech Note [Paclets Overview](https://reference.wolfram.com/language/tutorial/Paclets.html) in the Wolfram documentation describes the contents of this file and much, much more.

## Extending the documentation

Start with Wolfram’s introductory tech note on the [Documentation Tools](https://reference.wolfram.com/language/DocumentationTools/tutorial/DocumentationToolsQuickStart.html). Don’t forget to rebuild the paclet.

## Building the jsoupLink paclet

Speaking of documentation, the Paclet Tools also have a [Tech Note](https://reference.wolfram.com/language/PacletTools/tutorial/CreatingPaclets.html) that ties the various commands used for building and deploying together. For our limited purposes, it is not required reading.

Start a Mathematica session with a fresh kernel. Load the PacletTools package:

```mathematica
Needs["PacletTools`"]
```

Next, run

```mathematica
docresult = PacletDocumentationBuild["SOURCEPATH"]
result = PacletBuild["SOURCEPATH"]
```

This should (assuming all is well) produce two `Success[]` objects, like so:

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

At this point, there is no convenient single-file `jsoupLink-….paclet` yet, but you can already load the package directly from your file system (and unload it afterwards, e.g. to make more changes):

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

The result is the full path to the created paclet file, e.g., `"PROJECTPATH/jsoupLink-1.1.0.paclet"`. As an aside, the paclet archive file will be created as a sibling of the *SOURCEPATH*, making it reside in the *PROJECTPATH*. Feel free to copy or move this file to wherever you like, as it is fully self-contained.

## Installing and uninstalling the jsoupLink paclet

*Installing* the paclet makes it known to the Mathematica system; from this moment on, its contents can be found by `PacletFind["jsoupLink"]`, ``Needs["jsoupLink`"]``, ``<<jsoupLink`‍``, &c.:

```mathematica
PacletInstall["PROJECTPATH/jsoupLink-1.1.0.paclet"]
```

Installing involves copying the paclet contents to Mathematica-provided directories and to update the list of available packages; the paclet file itself is no longer required afterwards.

To remove the paclet, end all running Mathematica kernels with `Quit[]` and `run PacletUninstall["jsoupLink"]`. If the jsoupLink package has not yet been loaded in the current session, the kernel restart is not necesary.
