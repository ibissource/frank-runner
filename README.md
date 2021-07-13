# Frank!Runner

This project will help you run your Frank configurations with Tomcat.

Add a small build.xml to your project and run it to (re)start your Frank.


# Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Examples](#examples)
- [Specials](#specials)
- [Project structure and customisation](#project-structure-and-customisation)
- [Project per config](#project-per-config)
- [Debug property](#debug-property)
- [Frank!Framework version](#frankframework-version)
- [Other properties and software versions](#other-properties-and-software-versions)
- [How to add custom jars and classes](#how-to-add-custom-jars-and-classes)
- [Eclipse](#eclipse)
- [VSCode](#vscode)
- [Command line](#command-line)
- [Scripting](#scripting)

# Prerequisites
Make sure you have installed all of the following prerequisites on your machine:
- Git - [Download & Install Git](https://git-scm.com/downloads). Mac and Linux machines typically have this already installed. You can check this by running `git --version` in your console.
- cURL - Windows and Mac machines typically have this already installed. You can check if you have this already installed by running `curl --version` in your console.

## Windows
Install git by downloading the installer on https://git-scm.com/downloads

## Linux
To install git and cURL, run the following commands:
1. `sudo apt-get update`
2. `sudo apt-get install git`
3. `sudo apt-get install curl`

## Mac
The easiest way to install the prerequisites on Mac is by using [Homebrew](https://brew.sh).
Install Homebrew by running the following command in your terminal:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

When homebrew is installed, you can install git and cURL by running
`brew install git` and/or `brew install curl`

# Installation

## Rest of the installation

Clone or download this Frank!Runner project into the projects folder that
contains your Frank project(s) (make the frank-runner folder a sibling of your
project folder). You can now run build.xml files in projects that already have
them (like the example projects in frank-runner/examples). See the sections
[Eclipse](#eclipse) and [VSCode](#vscode) on how to use Eclipse and VSCode to
run a build.xml. When Tomcat has started you can browse to the following
address:

http://localhost

In case your project doesn't contain a build.xml yet you can add it to the root
folder of your project with the following content:

```xml
<project default="restart">
	<target name="restart">
		<basename property="project.dir" file="${basedir}"/>
		<condition property="exe" value="../frank-runner/restart.bat" else="/bin/bash"><os family="windows"/></condition>
		<condition property="arg" value="../frank-runner/restart.sh" else=""><os family="unix"/></condition>
		<exec executable="${exe}" vmlauncher="false" failonerror="true">
			<arg value="${arg}"/>
			<arg value="-Dproject.dir=${project.dir}"/>
		</exec>
	</target>
</project>
```

For Mac we need to use /bin/bash with restart.sh as an argument to work around:
Cannot run program "restart.sh" (in directory ".../FrankRunner/frank-runner"): error=2, No such file or directory

To make the items in the Last Tasks list of the Task Explorer unique you can
rename the target name restart in build.xml to something unique for your project
(e.g. restart-frank2yourproject) (make the value of the default attribute of the
project element the same).

You can also create a restart.bat with the following content which you can also
run from Windows Explorer:

```bash
call ..\frank-runner\ant.bat
if %errorlevel% equ 0 goto end
rem https://superuser.com/questions/527898/how-to-pause-only-if-executing-in-a-new-window
set arg0=%0
if [%arg0:~2,1%]==[:] if not [%TERM_PROGRAM%] == [vscode] pause
:end
```

And create a restart.sh with the following content to run on Linux or Mac:

```bash
#!/bin/bash
../../ant.sh
```

There are other ways possible to run the Frank!Runner scripts but to make it
easy for all project members and to have good integration with
[Eclipse](#eclipse) and [VSCode](#vscode) the preferred way is to use a
build.xml and optionally a restart.bat. Because the build.xml can be customized
and to keep all customasations in one place restart.bat calls ant.bat to run
the build.xml (instead of calling restart.bat).


# Examples

The Frank!Runner is shipped with the following examples


## Frank2Example1

Most basic Hello World example showing two configurations and a unit test. It
is referenced by the manual, for example at the following locations:

* https://frank-manual.readthedocs.io/en/latest/gettingStarted/examineExample.html#general-structure-of-the-example-frank
* https://frank-manual.readthedocs.io/en/latest/gettingStarted/helloIbis.html

## Frank2Example2

Some more Hello Wold adapters using both the DirectoryClassLoader and the
normal classpath. It is not referenced by the Frank!Manual.

## Frank2Example3

Example usage of message log, error store, message store sender and message
store listener. Description attributes have been added to the adapters which
can be read from the console. This will explain how to test and use the
adapters to be able to understand how the to e.g. resend messages.

This example is referenced by the Frank!Manual at the following location:

https://frank-manual.readthedocs.io/en/latest/operator/managingProcessedMessages.html#

## Frank2Example4

Example usage of Frank!Flow. In Frank2Example4 the Frank!Flow can be found at
the top of the Frank!Console menu. For information about the Frank!Flow go to:

https://github.com/ibissource/frank-flow#frankflow

Better example Frank! configuration files for Frank2Example4 to demonstrate
and / or test Frank!Flow are appreciated. Please create a pull request, an
issue or send an email with your improvements.


# Specials

To run the webapp, example and test modules of 
https://github.com/ibissource/iaf you can use the build.xml files in the
specials folder. As with other projects the iaf folder needs to be a sibling
folder of the frank-runner folder.


# Project structure and customisation

In case Frank2YourApplication contains a pom.xml it is assumed to be a Maven
project and the following default values are used:

```properties
classes.dir=src/main/resources
configurations.dir=src/main/configurations
tests.dir=src/test/testtool
context.xml=src/main/webapp/META-INF/context.xml
```

Otherwise:

```properties
classes.dir=classes
configurations.dir=configurations
tests.dir=tests
context.xml=context.xml
```

Hence by default your folder structure will need to look like the following (for
non-maven projects):

```
|--projects
   |--frank-runner
   |--Frank2YourApplication
      |--classes
      |--configurations
         |--Config1
         |--Config2
         |--Conf...
      |--tests
      |--build.xml
      |--restart.bat
   |--Frank2YourOtherApplication
      |--...
```

You can overwrite default values by creating a frank-runner.properties in the
project to run. In case you need to change the default value for projects.dir
(which is ..) you can create a build.properties in the frank-runner folder
(which can also be used to specify the project.dir as explained earlier).

Your project doesn't need to contain a context.xml in case you want to use H2.

When changing files in the classes folder you need to restart Tomcat. When
changing files in the configurations folder you need to reload the configuration
in the Frank!Framework console (or restart Tomcat).

More information on Frank configuration files and Frank property files and how
to use them can be found in the
[Frank!Manual](https://frank-manual.readthedocs.io/).


# Project per config

In case your application comprises several Frank!Configurations and you would
like to have a project per configuration (e.g. to give each configuration it's
own CI/CD pipeline) the following setup would be possible and is automatically
detected by the Frank!Runner based on the presence of the war/pom.xml:

```
|--projects
   |--frank-runner
   |--frank2application
      |--ear
      |  |--src
      |  |  |--...
      |  |--pom.xml
      |--war
      |  |--src
      |  |  |--...
      |  |--pom.xml
      |--build.xml
      |--pom.xml
      |--restart.bat
   |--frank2application_config1
      |--src
      |  |  |--main
      |  |     |--configuration
      |  |        |--Config1
      |--build.xml
      |--pom.xml
      |--restart.bat
   |--frank2application_config2
      |--...
   |--frank2application_config3
      |--...
```

The build.xml in the config projects need to have to following content (see
section [Installation](#installation) for the content of the build.xml that
should be added to the main project) (you can rename target restart to
restart-&lt;projectname&gt; to have better overview on the Last Tasks list of
the Task Explorer):

```xml
<project default="restart">
	<target name="restart">
		<basename property="project.dir" file="${basedir}"/>
		<split projectdir="${project.dir}"/>
		<condition property="exe" value="../frank-runner/restart.bat" else="/bin/bash"><os family="windows"/></condition>
		<condition property="arg" value="../frank-runner/restart.sh" else=""><os family="unix"/></condition>
		<exec executable="${exe}" vmlauncher="false" failonerror="true">
			<arg value="${arg}"/>
			<arg value="-Dmain.project=${main.project}"/>
			<arg value="-Dsub.project=${sub.project}"/>
		</exec>
	</target>
	<scriptdef language="javascript" name="split">
		<attribute name="projectdir"/> 
		var projectDir = attributes.get("projectdir");
		var i = projectDir.indexOf('_');
		project.setProperty("main.project", projectDir.substring(0, i));
		project.setProperty("sub.project", projectDir.substring(i + 1));
	</scriptdef>
</project>
```

This way every (configuration) project can be started and tested by it's own.


# Debug property

Add the following to either a build.properties in the frank-runner folder or a
frank-runner.properties in the root folder of your project to make the
Frank!Runner display the most important properties just above the Frank!Runner
ASCII art:

```properties
debug=true
```


# Frank!Framework version

By default the Frank!Runner will check once every hour at startup whether a new
Frank!Framework version is available and download and use the latest
Frank!Framework version. This means you are running a Frank!Framework version
with the latest and greatest features but also a version that did not yet pass
all quality checks yet. Hence, you might run into a bug in the Frank!Framework.
Please report any Frank!Framework bugs at:

https://github.com/ibissource/iaf/issues

We appreciate your help but in case you would like to use a more reliable
version and/or like to disable the update mechanism add the following to either
a build.properties in the frank-runner folder or a frank-runner.properties in
the root folder of your project:

```properties
update.strategy=none
```

Or specify a specific Frank!Framework version like:

```properties
ff.version=7.4
```

Or:

```properties
ff.version=7.6-20200306.163142
```

Check the following url to see all available Frank!Framework versions:

https://nexus.frankframework.org/repository/public/org/ibissource/ibis-adapterframework-webapp/maven-metadata.xml

Please note that very old versions might not run correctly with the
Frank!Runner.


# Other properties and software versions

At the top of the build.xml (in the init target) you will find a lot of
properties which you can override in either a build.properties in the
frank-runner folder or a frank-runner.properties in the root folder of your
project.

You could for example specify a different JDK, Ant or Tomcat version. E.g. for
Tomcat use:

```properties
tomcat.version=7.0.100
```

Please consider making a pull request in case you find a newer software version
to keep the Frank!Runner up to date with the latest software versions.

In some cases you might want to run on a different port and/or context. E.g. to
run on:

http://localhost:81/test

Use:

```properties
tomcat.connector.port=81
context.path=test
```

In case you have another Tomcat running on port 80 (possible using another
Frank!Runner instance) you probably also want to change the tomcat.server.port
which defaults to 8005 to prevent a conflict between the two Tomcat instances.
E.g. use:

```properties
tomcat.server.port=8105
```


# How to add custom jars and classes

In case you need to (for example) add your own pipe which is not available in the
Frank!Framework or you would like to customize an existing pipe in the
Frank!Framework you can add a java folder to the project with your custom .java
 file(s).

For a Maven project the default value for the java folder is:

```properties
java.dir=src/main/java
```
Otherwise:

```properties
java.dir=java
```

For custom jar files create the following folders:


```properties
lib.server.dir=lib/server
lib.webapp.dir=lib/webapp
```

All jars added to lib/server are copied to the Tomcat lib folder. All jars added
to lib/webapp are copied to the WEB-INF/lib containing the Frank!Framework jar
files and dependencies.


# Eclipse

Choose one of the methods described in the sections below to run the build.xml
and/or restart.bat in your project. See section [Installation](#installation)
for more information on the build.xml and restart.bat and how to add them to
your project in case they don't exist yet.

## Ant

Right click on build.xml, Run As, Ant Build. The second time you can use the run
button on the Toolbar.

## Terminal

Open the terminal view, cd to your project and execute restart.bat.


# VSCode

Choose one of the methods described in the sections below to run the build.xml
and/or restart.bat in your project. See section [Installation](#installation)
for more information on the build.xml and restart.bat and how to add them to
your project in case they don't exist yet.

## Task Explorer

Install plugin Task Explorer and configure it to use ant.bat or any other
Ant installation by filling "Path To Ant" in the Extension Settings of Task
Explorer with:

C:/path/to/frank-runner/ant.bat

Backslashes will also work except when your VSCode is configured to use a bash
shell.

Disable the "Enable Ansicon For Ant" option. Now you can use Task Explorer to
either run the build.xml or the restart.bat in your project.

Below the file explorer open the Task Explorer, select restart below ant or
restart.bat below batch and click the Run button.

## Task Explorer with Ansicon

It is also possible to install Ansicon and keep Ansicon enabled in the Task
Explorer plugin but your system might think it is malwar:

https://github.com/adoxa/ansicon/issues/30

You can download Ansion at:

https://github.com/adoxa/ansicon/releases

Fill "Path To Ansicon" in the Extension Settings of Task Explorer with the path
to Ansicon (and keep the "Enable Ansicon For Ant" option enabled) in case you
would like to use Ansicon.

## Ant Target Runner

Install plugin Ant Target Runner and configure it to use ant.bat or any other
ant installation by adding the following to settings.json:

```json
"ant.executable": "C:\\path\\to\\frank-runner\\ant.bat",
```

You might need to restart VSCode for the Ant Target Runner plugin to detect the
build.xml in the project you have opened. Below the file explorer open the Ant
Target Runner, select the restart target and push the Run Selected Ant Target
button. Please note that the Ant Target Runner will only be able to use the
build.xml in the root of the currently opened folder/workspace.

## Terminal

Right click restart.bat in your project, click Open in Terminal and execute:

```
projects\your-project> .\restart.bat
```

## Task

In the root of the opened folder create a new folder called .vscode and within
the new .vscode folder create a tasks.json with the following content:

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Restart",
            "type": "shell",
            "command": "./restart.bat",
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
}
```

This will make it possible to restart using ctrl-shift-B (or go to Terminal,
Run Task...).


# Command line

For Frank!Runner command line usage execute the commands below either from the
Windows Command Prompt, Windows PowerShell, a terminal in Eclipse, VSCode or any
other terminal.

In case you did not already clone or download Frank!Runner and you have the git
command available (otherwise download it manually or use another Git program to
clone the project):

```sh
projects> git clone https://github.com/ibissource/frank-runner
```

Change directory to frank-runner:

```sh
projects> cd frank-runner
```

On Windows run the following command:

```
projects\frank-runner> .\run.bat
```

And on Linux or Mac run:

```
projects\frank-runner> ./run.sh
```

You can now browse to the following address to find the Frank!Framework
console:

http://localhost

You can stop Tomcat using the following combination of keys: 

```
ctrl-c
```

Instead of the run script (.bat or .sh) you can also use the start, stop and
restart scripts which will return after being executed (opening Tomcat in a
separate window):

```
projects\frank-runner> .\start.bat
```

```
projects\frank-runner> .\stop.bat
```

```
projects\frank-runner> .\restart.bat
```


# Scripting

You can call Frank!Runner from a script. Any parameter passed to Frank!Runner
is passed to Ant and Tomcat. For instance, to create an instance with a specific
Frank!Framework version (7.2 in the example), logging to a specific log
directory, the following call can be made:

```
path\to\frank-runner\start.bat -Dff.version=7.2 -Dlog.dir=logs/7.2
```

This will create the instance, deploy on Tomcat, start Tomcat and return to the
script. To shutdown Tomcat, call stop:

```
path\to\frank-runner\stop.bat
```
