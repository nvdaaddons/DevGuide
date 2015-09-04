<title>NVDA add-on Development Guide</title>

# NVDA Add-on Development Guide

Author: Joseph Lee and contributors
Latest version: August 2015 for NvDA 2015.3

Welcome to NVDA Add-on Development Guide. This is the one-stop guide on how NVDA add-ons are developed, as well as explaining some useful code segments from NVDA core source code useful when writing add-ons.

For more information on NVDA development, please visit [NVDA Community Development page][1]. Be sure to go over [NVDA Developer Guide][2] to familiarize yourself with key terms and basics on getting started with add-on development.

## Audience ##

This guide is designed for both beginners who are new to Python and/or NVDA development in general, as well as experts, power users and programmers who are familiar with Python or other programming languages and/or familiar with NVDA source code structure.

If you are new to NVDA add-on or core development, we recommend that you get to know Python first, as it gives necessary programming background for understanding the rest of the guide. If you are a Python programmer but are new to NVDA development, please checkout NVDA development guide and Design Overview document, both of which can be found on the NVDA Community website under development section.

### Authors, contributions and copyright ###

This guide was originally written by Joseph Lee and is shaped by NVDA user and developer community. We welcome your feedback and contribution.

Copyright: NVDA is copyright 2006-2015 NV Access. Microsoft Windows, Microsoft Office, Win32 API and other MS products are copyright Microsoft Corporation. IAccessible package is copyright IBM and Linux Foundation. Python is copyright Python Foundation. Other products mentioned are copyrighted by authors of these products.

## System requirements ##

To create an add-on for NVDA, please make sure the following system requirements are met:

* A version of NVDA is available on your computer (either a portable or installed version will work, but we strongly recommend that you install a copy of NVDA on your development computer). Download NVDA from NV Access page at http://www.nvaccess.org.
 * We recommend installing the latest master development version to keep up to date with core API changes. You can download the latest snapshots at http://community.nvda-project.org/wiki/Snapshots.
* Python 2.7 series, version 2.7.10 32-bit for Windows: <http://www.python.org/download/releases/2.7.10/>
* SCons 2, version 2.3.0 for generating add-on packages: <http://www.scons.org/>
* Markdown 2.0.1 or later for generating add-on documentation: <https://pypi.python.org/pypi/Markdown/2.0.1>
* GNU Gettext package for Windows for message localization support. The build can be found at: <http://gnuwin32.sourceforge.net/downlinks/gettext.php>
 * Once downloaded, copy both exe files to your add-on development folder.
* Git 1.7.9 or later if you wish to upload the add-on to a repository such as Bitbucket (optional, see below). You can use various Git clients, such as Git Bash, Cygwin's Git, Tortoise Git and so forth.
* The NVDA Community Add-on Template for ease of add-on file and folder packaging and management (optional; [click here][3] to download the add-on template).
* Additional Python modules or dependencies for your add-on.

## What are add-ons? ##

An add-on is an additional package that extends NVDA's functionality or support for programs. This may include adding global features, enhancing support for an application or adding support for newer braille displays or speech synthesizers.

Currently, the following add-on modules are supported. Note that an add-on may include more than one add-on modules such as a global plugin and a speech synthesizer packaged into one add-on.

* Global plugin: A global plugin adds features for NVDA which can be used anywhere, such as OCR capability.
* App module: An app module allows enhanced support for a program, such as specific windows or controls of a program such as audio editors.
* Driver: A driver allows a program to talk to hardware. Currently one can write drivers for new braille displays or speech synthesizers.

Each NVDA add-on package (composed of one or more add-on modules) is a zip file with the file extension of .nvda-addon. These can be installed via Add-ons Manager, found in NVDA 2012.2 or later. Alternatively, one can install them from file manager if one uses NVDA 2012.3 or later installed on the computer.

Throughout this guide, we refer to standard NVDA modules which comes with NVDA as "NvDA Core" to distinguish it from add-on modules.

## Setting up the add-on development environment ##

Follow these steps to prepare your computer for writing NVDA add-ons.

### Installing dependencies ###

1. If you don't have NVDA, download and install NVDA from NV Access website.
2. Install Python 2.7.x 32-bit on your computer (if using 64-bit Windows, install using the 32-bit Windows installer package).
3. Install Markdown and SCons using their Windows installer packages.
4. If you plan to share your add-on code with others, install Git clients.
5. Paste the Gettext executable files to your add-on development folder (see the next section on add-on development folder).
6. If you are developing support for a program, speech synthesizer or a braille display, install the needed software and/or have the hardware handy.

### The add-on development folder ###

When writing add-ons, it is recommended that you store your add-on code in separate folders, one per add-on. If you chose to download the add-on template, the folder structure will be automatically created.

Once you install the needed dependencies (see above), paste the Gettext package executables into this add-on folder.

### Add-on folder structure ###

Each add-on folder, at a minimum, must contain the following files and folders:

 * manifest.ini to store manifest information such as add-on name and author.
* An "addon" subfolder with the add-on module directory underneath this subfolder (appModules, globalPlugins, synthDrivers, brailleDisplays). One or more module folders can be specified.

If you are using the add-on template, the folder structure will automatically be created, so you need to create only the addon subfolder and the add-on module folder(s) and code inside this folder. See the readme file in the template folder for more information on customizing your add-on manifest using the template files.

### Packaging add-ons ###

There are two ways of packaging add-ons:

1. To package your add-on manually, zip up (compress) your add-on folder as a .zip file, then rename the file extension to .nvda-addon.
2. To use the add-on template with SCons, open Command Prompt with administrator mode (Windows Vista or later), change to your add-on folder and type `scons~.

For more information on add-on management, see the management chapter in this guide.

## Getting started: Hands-on examples ##

So are you ready to start your adventure with add-ons, but not sure as to how to bring it to life? If that is you, please go through this chapter, as it gives you basic information to get you started with add-ons and give you tips on writing code.

Note: for this chapter, we will not use the actual add-on packages. Instead, we'll use plugin folders - a number of subdirectories in your NVDA user configuration folder (available from Start Menu/Screen if NVDA is installed) to store our example Python files.

To edit .py files, you need a word processor which can handle .py files. The best one we recommend is Notepad++.

### How add-on code is organized ###

 Your add-on code is stored in one or more Python files (.py file). Despite different kinds of add-ons out there, they all have similar layout.
 
 First, you start by writing an optional header for your add-on, such as your name, a brief sentence or two on what the add-on is for and so on. Although this is optional, it is recommended that you write the header as a reminder to keep track of what you are doing with your add-on.
 
 Next, you tell NVDA the modules you need for your add-on file. This is done by writing `import module` where module is the name of the module you wish to use in your code. For example, if you want to hear tones while writing your add-on, write `import tones`. Typically you may need to import two or more modules for your specific add-on (see below on list of modules you need for the type of add-on module you are writing).
 
 after declaring the modules you need or import, you write your add-on code (defining classes, variables, methods and so on). The most important section is the add-on class code, which will determine the type of add-on module your code will be assigned to.
 
 For instance, if you wish to add support for a program, after importing appModuleHandler and other needed modules, you would write:
 
 `class appModule(appModuleHandler.AppModule):`
 
After that, all you are writing is Python code (see the Python documentation on how to write Python programs).

### Running your add-on in this example chapter ###

To run your example add-ons from this chapter, open your NVDA user configuration directory (from Start Menu/Screen, look for Explore NVDA user configuration folder" item). Then paste your .py file to the appropriate folder: appModules folder for app module examples, and globalPlugins folder for global plugins.

### Example 1: Hear a tone when pressing NVDA+A ###

Let us start with a simple example: if you press NVDA+A, you would hear a tone for 1 second from any program. Since we want to use this everywhere, it must be a global plugin.

First, open your user configuration folder, then open globalPlugins folder. Create a new .py file and give it a descriptive name such as example1.py (it is strongly recommended that when you name your global plugin file, give it a short descriptive name). Then open the newly created .py file in the word processor.

The below code implements our example. Put this in your .py file as exactly as shown:

	# Add-on development first example
		
	import globalPluginHandler
	import tones # We want to hear beeps.
	
	class GlobalPlugin(globalPluginHandler.GlobalPlugin):
		
		def script_doBeep(self, gesture):
			tones.beep(440, 1000) # Beep a standard middle A for 1 second.
		
		__gestures={
			"kb:NVDA+A":"doBeep"
		}

In Python, you put comments by putting hash sign (#) at the start of the comment line.

### Example 1 code explanation ###

Our first example let's us sound a beep for one second when we press NVDA+A. But you might be wondering what that above code means, so let's step through the code, one piece at a time.

1. At the top of the file, we wrote a header which tells us that this is an example add-on.
2. Since this is a global plugin, we need to import a crucial module: global plugin handler, so we wrote `import globalPluginHandler`.
3. Then we wrote `import tones` to import (load, or include) the tones module, a built-in module from NVDA. Whenever you wish to use a method from a given module, import the needed module(s).
4. Next, we defined a class called GlobalPlugin. The text inside the brackets tells us where this class is coming from (more on this concept in a second). A class, in programming, describes an object, such as a person, a desk, a program and others.
5. Inside the class, we wrote a method (function) called `script_doBeep`. This is an example of a script, a method that'll be run or executed when you press a command. Inside this script, we wrote `tones.beep(440, 1000)` to tell NVDA to sound a middle A tone for 1 second. In programming, a function can take arguments, or a set or parameters which tells the function what to do based on the given values (we'll meet them later). In fact, many methods you'll be writing, including our doBeep script takes one or more arguments. More on scripts later as we journey through the guide.
6. Lastly, we wrote a simple dictionary (a collection) to store our command (script) bindings for our doBeep script. Here, we told NVDA to assign NVDA+A command for doBeep script.

Save this file, then restart NVDA. Now whenever you press NVDA+A, you'll hear a middle A tone for 1 second. Once you are comfortable with the add-on code and how it is laid out, you can delete the newly created .py file.

### I don't understand those above terms ###

For some, the terms "class", "method" and so on might be new. Let's go over what these terms are, as they are fundamental for add-on development:

* Class: a class describes an object. It could describe anything, such as a person, a desk, an NVDA add-on and others. Classes are fundamental to NVDA and other programs - in fact, a number of programmers are skilled at coming up with classes.
* Method: A method is a short program or a routine that a program runs for doing something, such as generating tones, calculating huge numbers, loading NVDA add-ons and so on. Some people call them "functions."
* Script: A script is a method which runs when the user performs commands such as pressing certain keys on a keyboard. For example, when you press NVDA+F12, NVDA runs dateTime script, located in one of the NVDA core modules named Global Commands. A script takes two arguments: where the script would be executed (usually "self"; more on that later) and the gesture for the script (see below).
* Variable: A variable is something that can change, such as name of a person, name of the NVDA add-on we're running, version of NVDA you are using and so on. An add-on file may define one or more variables (for example, to store common constants such as strings).
* Module: A module is a collection of methods and variables in a file. When we write add-ons, we are in fact writing additional modules that NVDA can use while it is running.

There are other terms that we'll get to know shortly.

### Example 2: Generate a tone when switching to Notepad ###

Most of the below code comes from NVDA Developer Guide.

Not only NVDA let's you add global commands, but it also allows writing code to enhance usage of programs through app modules. An app module is also a Python file except that, this time, the name of the .py file is the name of the executable for a program. For example, an app module for Notepad would be named notepad.py.

The below code, from NVDA developer Guide, gives a short example of a typical app module: play a short beep when switching to Notepad. Put the below code in notepad.py, which in turn should be placed in appModules folder in your user configuration folder in order for it to run.

	# An example app module.
	
	import appModuleHandler
	import tones
	
	class AppModule(appModuleHandler.AppModule):
		
		def event_gainFocus(self, obj, nextHandler):
			tones.beep(256, 200)
			nextHandler()

### Example 2 code explanation ###

We're seeing more new code here. Let's go over this, again piece by piece:

1. Unlike the first example, the crucial module we need is appModuleHandler.
2. The class that we are using is AppModule.
3. Unlike last time, we're using events, a method run when certain events occur such as when names of controls change. Events take an object as one of its arguments, the object for which the event needs to be dealt with, or, as many people say, "fired."
4. Inside the event method, we're also seeing a call to `nextHandler`. This method is used in event methods to tell NVDA to pass the event so it can be taken care of, such as saying the name of a control after beeping.

### More new terms please ###

Other terms you may see include:

* Event: An event is a method that'll be run when some events happen, such as when a program is on focused, when names of controls change and so on.
* Call: We say a function calls some another method when we run the second method from another method. For example, in our first example, we're calling tones.beep method from our script method.
* Object: An object is an instance of a class - that is, a class coming to life when a program runs. Throughout your add-ons, as you write classes and when you run your add-ons, your classes come to life as objects, commonly abbreviated to obj. In NVDA, an object may refer to controls or parts of a program.
* Self: In Python, the word "self" means current class (if we're defining one, such as when writing add-ons), or means the class for which a method is defined. For example, in a class called numbers, the add method would have self as the first argument, reminding us that add method is part of the class of numbers. In NVDA development world, self usually means the current NVDA object (see below), or in the add-on development, the instance of an add-on. Many of your methods will have self as the first argument.

Just like example 1, once you're comfortable with app module code, you may wish to delete the Notepad app module code unless you want to keep hearing beeps when you switch to Notepad. The actual differences between global plugins and app modules will become more clear when we talk about them in more detail throughout this guide.

### A few tips for beginners ###

Here are a few useful tips passed on by add-on writers:

* Start with easy add-ons, such as saying a message, tones and so on.
* Write and test one method at a time.
* If you are writing app modules or drivers, become familiar with programs, synthesizers or braille displays you wish to support (e.g. read documentation, try using them, etc.).
* When defining commands (especially in global plugins), consult commands used in NVDA and other add-ons first before assigning a new command in your add-on to avoid command conflicts.

## Useful modules from NVDA core ##

Throughout the life of add-on development, you'll come across some useful modules from NVDA core that would be helpful in your add-on code. This section explains them and some functions in those modules that would be useful.

### List of useful NVDA core modules and methods ###

The following lists available NVDA core modules and some useful methods found in those modules:

* Addon Handler (addonHandler.py): The module which implements the add-on subsystem. The addonHandler.initTranslation() method is used to initialize internationalization support for your add-on.
* NVDA basic API (api.py): A collection of core methods used throughout NVDA, such as obtaining focus and navigator object, setting focus and so on. See the next list on useful methods from this module.
* App Module subsystem (appModuleHandler.py, appModules): The subsystem in charge of handling app modules (see chapter on app modules for more information).
* ARIA support (aria.py): Implements support for Accessible Rich Internet Applications (ARIA).
* Base object collection (baseObject.py): Contains useful base objects such as scriptable objects (see the chapter on NVDA objects and overlay objects for more information).
* Braille input and output subsystem (braille.py, brailleInput.py): Controls braille output to and input from braille displays, needed by braille display driver add-ons.
* Build-in modules (builtin.py): Allows access to builtin modules when working with add-ons.
* Configuration (config): Manages configuration and profiles (profiles are available in 2013.3 or later).
* Controls and states collection (controltypes.py): Includes dictionaries on control types (roles) and possible states that a control can be in.
* Events (eventHandler.py): Handles various events such as gaining focus.
* Global Commands collection (globalCommands.py): A list of global commands available while using NVDA (see section on script scope for more information).
* Global Plugin subsystem (globalPluginHandler.py): The module needed for controlling global plugins.
* NVDA GUI (gui): A collection of classes used by NVDA to display its messages graphically. Includes GUI's for NVDA menu, add-on manager and others.
* Hardware port utilities (hwPortUtils.py): A set of utilities for communicating over serial and other hardware ports, useful during driver add-on development.
* IAccessible support (IAccessibleHandler.py, IAccessible objects): Used for supporting IAccessible controls.
* Input management (inputCore.py): Manages input from the user.
* Java Access Bridge support (JABHandler.py): A collection of methods used for supporting JAB subsystem used for Java applications.
* Keyboard input (keyboardHandler.py): Supports entering commands from the keyboard.
* Logging facility (logHandler.py): Allows a module to write logs to be viewed by a developer or a user via Log Viewer.
* Math content presentation (MathPress packages): allows NVDA to recognize and interact with various math content and markup. NvDA ships with MathML support package and support for Math Player is included in 2015.2 or later.
* Mouse support (mouseHandler.py): Supports mouse commands.
* NVDA objects collection (NVDAObjects): A collection of NVDA objects or controls used in many applications and standards such as UIA (User Interface Automation). Some objects require special actions to be performed, and these are specified in behaviors module in NvDA objects package.
* Review facility (review.py): assists with working with review cursor.
* Scripts support (scriptHandler.py): Handles scripts, methods executed due to the user pressing keyboard commands and other input.
* Speech output (speech.py): Controls speech output.
* Synthesizer driver support (synthDriverHandler.py): This is the core module needed for speech synthesizer add-ons.
* Widget text access (textInfos): Allows access to text for widget and documents.
* Touchscreen support (touchHandler.py): Provides support for touchscreen input (installed versions only).
* Tone output (tones.py): Allows the user to hear tones.
* User interface messages (ui.py): Includes ui.message method used to speak or braille certain text.
* UIA support (UIAHandler.py, UIA objects): Used for supporting UIA (User Interface Automation) controls.
* Virtual buffers (virtualBuffers): Handles virtual buffer documents such as websites.

The modules without .py extension are directories, containing specialist modules. In addition, NVDA provides wrappers for Windows libraries (DLL's) such as Windows User (user32.dll wrapped in winUser.py), Windows Kernel (kernel32.dll wrapped in winKernel.py) and so on used in some specialist situation when using Windows API is a must (such as sending messages back and forth in an app module).

### Useful methods ###

Here is a list of some useful methods used in add-ons. For more information on how they're implemented, see the NVDA source code documentation. For worked out examples, see the section of this guide on add-on components.

From addonHandler:

* `addonHandler.initTranslation()`: Sets up the translation subsystem for add-ons via Gettext.

From api.py:

* `api.getFocusObject()`: Retrieves the focused control (returns the object with focus).
* `api.getNavigatorObject()`: Fetches the current navigator object. If NVDA is set to follow system focus, the focus and navigator object will be the same, otherwise a different object is returned.
* `api.getForegroundObject()`: Returns the foreground window of the current application (the parent of this object is the application itself).
* These have a corresponding method to set certain object as the focus or navigator object. Note that these lets NVDA see the new focus or navigator object but does not actually change system focus.

From logHandler:

* `logHandler.Log`: The class which implements logging facility.

From tones:

* `tones.beep(pitch in hertz, duration in milliseconds, left channel volume, right channel volume)`: Plays a tone of specified pitch for specified duration. The first two arguments are mandatory, while the other two are optional.

From ui:

* `ui.message(message to be spoken/brailled)`: Speaks or brailles the message. This should be a string surrounded by quotes.

There are other useful methods out there, but the above are the most useful ones. See the NVDA source code documentation for other methods, or see the examples below on how these methods and others are used throughout the life of an add-on.

## Add-on module components and development tips ##

An add-on module consists of a number of components. This includes handling input and output, working with different NVDA objects, reacting to events, storing configuration and more.

This chapter introduces key components and concepts that are used in add-on development, such as NVDA objects, scripts, event handling and additional topics with examples.

Note that the NVDA core development guide introduces the below concepts. This chapter is intended as an extension of that document. Consult the NVDA development guide for a brief introductions.

### Working with objects on screen ###

An object is an instance of a class - that is, a class coming to life while a program is running. For example, if a class called button has been defined, the button on a screen is the object of this button class.

In NVDA, an object is representation of a control or parts of a program. This includes buttons, check boxes, edit fields, toolbars, sliders and even the application window. These are organized into hierarchies, or parent-child relationship where an object may contain child objects - for example, a list object in Windows Explorer may contain one or more list items, and the parent of this list might be the Windows Explorer window. The object that you're examining right now is termed "navigator object."

The NVDA objects (or simply called objects) contains a number of useful properties or atributes. These include the object's name, its value (checked, text of the edit window, etc.), role (check box, window, embedded object, etc., location (screen coordinates) and more. NVDA objects also contain useful methods for manipulating them, such as changing the value of the object, reacting to events for the object (gains focus, value changed, etc.) and so on.

In many situations, an NVDA object may belong to a class of related objects. For each object classes, NVDA provides ways of handling them. These classes include IAccessible, JAB, UIA and so forth. These classes and behaviors for each class of objects is defined in NVDAObjects directory in the NVDA source code, and to use them in your add-on, import the appropriate object class handler for the object you're using (e.g. if you're working with an IAccessible object, import NVDAObjects.IAccessible.).

Two of these object classes merit special mention: virtual buffers and tree interceptors. A tree interceptor allows NVDA to work with a "tree" of objects as though they are just one object. A special case of tree interceptor is virtual buffer, which allows NVDA to work with complex documents such as PDF documents. These objects contain a special mechanism to determine whether a given keyboard command will be passed to the application or handled by NVDA (for instance, browse mode where first letter navigation is used to move between elements).

### Examining object hierarchy ###

There are a number of ways which you can use to see the hierarchy of an object for a given program:

1. Using object navigation commands (NVDA+Numpad 2/4/5/6/8) with simple review mode turned off.
2. Using Python Console, use obj.next/previous/parent/firstChild/lastChild attributes. If you want to see all available properties, from Python Console, type dir(obj).

If you wish to see a more detailed description about the navigator object, while the navigator object is located at the object you're interested in, press NVDA+F1 to launch log viewer. The root of all objects in Windows is the desktop, or shell object.

### Focus vs. navigator object ###

In your add-on, you might wish to work with various objects and manipulate them. These may include changing the focused object, synchronizing navigator and focus objects, changing the role of an object and so on.

A focus object is the currently focused control. These are linked to keyboard focus - that is, it follows the highlighted control. In contrast, a navigator object is the object you're interested in. Since navigator objects can move anywhere, you can examine two objects at once: the focused object and the navigator object. For instance, you might be focused on an edit field while examining the title bar as the navigator object.

In your add-on, to fetch the object with focus, write `someObj = api.getFocusObject()`. The someObj can be named differently - the convention is to use the name "obj". To fetch the navigator object (which might be different from the focused object), use `obj = api.getNavigatorObject()`.

### Other useful object-related goodies ###

Here are some other methods which works with NVDA objects, all located in api.py module:

* If you wish to obtain the foreground object (useful if you wish to look at some child object of the foreground window), use `obj = api.getForegroundObject()`. The name of the foreground object, usually the top-level window of an application is treated as a title by NVDA and can be obtained by pressing NVDA+T.
* From Python Console, to see the number of child objects that an object contains (for instance, the children, or widgets of a foreground window), type `obj.childCount`. The value 0 means that there are no more child objects.
* To set some object as the new focus or navigator object, use `api.setFocusObject(obj)` or `api.setNavigatorObject(obj)`. These do not change what Windows views as focused object, as these change what NVDA thinks is the focus and navigator object.
* You can fetch various properties of an object by specifying obj.property where property is the attribute you wish to see (e.g. obj.value).

### Example 1: Finding the value of a slider in a program ###

Suppose you are asked by a user to give him the value of a slider in a program using an app module. After looking at the object hierarchy and other properties, you know that the toolbar is the last child of the foreground object.

Here is the code to implement this feature:

	# Object example 1
	
	import api
	import appModuleHandler
	
	class AppModule(appModuleHandler.AppModule):
		
		sliderChildIndex = -1 # The variable to store the child index.
		
		def getSliderValue(self):
			fg = api.getForegroundObject()
			sliderVal = fg.children[self.sliderChildIndex].value
			return sliderVal

In this code, the method `fg.children[index]` is used to retrieve the child with the given index (here, since we said the toolbar is the last child, the index would be minus 1, or the very last child; we could have used fg.lastChild).

However, this code has an issue: what if the slider value is actually within the first child of the actual slider control? One way to fix this is to check the object's role. The modified code looks like this:

	def getSliderValue(self):
		from controltypes import ROLE_SLIDER # It is possible to import from within a method.
		fg = api.getForegroundObject()
		slider = fg.lastChild
		if slider.role == ROLE_SLIDER: return slider.firstChild.value

Thus, when we know for sure that we're dealing with the slider, the method returns the value of the slider's first child (if that is the case). Note the two equals signs for equality, as opposed to just one equals sign for assignment.

There are other examples you can try to familiarize yourself with object navigation and manipulation:

* Obtaining the name of an object that is located somewhere else in the program.
* Moving the navigator to the foreground object.
* Setting focus to another program.

For real-life examples on objects in NVDA, consult the NVDA source code or source codes of various community add-ons.

### Specialist objects and overriding object properties at runtime ###

Sometimes, it is not enough to work with default behavior for a control. For example, some parts of a program may need custom gestures, or one may need to change the role of a window to that of a button.

NVDA provides two methods for creating specialist, or overlay objects (or classes), each suited for different needs:

* `event_NVDAObject_init(self, object we're dealing with)`: If you wish to override certain attributes of a control such as its role or label (name), you can use this method to ask NVDA to take your "input" to account when meeting objects for the first time (or initialized). For instance, if the control has the window class name of TForm (seen on many Delphi applications), you can ask NVDA to treat this control as a standard window by assigning obj.role = ROLE_WINDOW (see control types dictionary for list of available roles).
* `chooseNVDAObjectOverlayClasses(self, object, list of classes)`: This allows NVDA to use your own logic when dealing with certain objects. For example, this is useful if you wish to assign custom gestures for certain parts of a program in your app module (in fact, many app modules creates objects to deal with certain parts of a program, then uses chooseNVDAObjectOverlayClasses to select the correct object when certain conditions are met). These custom objects must be based on a solid object that we wish to deal with (mostly IAccessible is enough, thus most overlay objects inherit from, or is the child or specialist class of IAccessible objects). In certain situations, you can use this method to drop a property from an object, such as telling NVDA to not treat this object as a progress bar by removing progress bar behavior from this object.

Note that in case of the second method, the class(s) with the given name must be present in the file, which is/are inherited from a known base object (in Python, the syntax for the inheritence is `childClass(baseClass)`, and is usually read as, "this child class inherits from this base class". We'll see code like this later).

### Examples of overlay classes and modified roles ###

Below examples illustrate the uses of the two overlay and attribute modification methods we've discussed above:

An example of the first case: modifying an atribute.

	# Reassign some Delphi forms as window.
		def event_NVDAObject_init(self, obj):
			if obj.windowClassName == "TForm": obj.role = ROLE_WINDOW

This means that whenever we encounter a window with the class name of "TForm", NVDA will treat this as a normal window.

Example 2 deals with an app module which has two objects for dealing with specific parts of a program, then uses chooseNVDAObjectOverlayClasses to assign the logic for each control.

	#An example of overlay classes
	
	class enhancedEdit(IAccessible):
		# Some code to be run when window class name is MyEdit.
	
	class MainWindow(IAccessible):
		# Another code, this time adding custom gestures for main window of the program.
	
	# In the app module:
	
	def chooseNVDAObjectOverlayClasses(self, obj, clsList):
		if obj.windowClassName == "myEdit": clsList.insert(0, enhancedEdit)
		elif obj.windowClassName == "TWindow": clsList.insert(0, mainWindow)

In both cases, the object that we wish to check must be inserted as the first element of the clsList. The effect is that these custom objects will take precedence when looking up gestures or code (behavior) for the object, and in the developer info, these custom objects will come first when MRO (Method Resolution Order) for the navigator object is displayed.

Note: You may need to tune these two methods to provide correct overlay classes for very specific controls (such as checking names, specific roles, etc.), otherwise you may find that two or more identical-looking controls are assigned to your custom object when in fact they are very different. Also, the event_NVDAObject_init is only available in app modules.

### Input and output: scripts and UI messages ###

Another crucial component of add-ons is handling commands from users and displaying what the add-on is doing. These are done via scripts (input) and UI messages (output).

A script is a method run when the user performs certain commands. For example, when you press NVDA+T, NVDA runs a script in global commands module called SayTitle. In Poedit, for instance, when a translator presses Control+Shift+A, NVDA will read translator comments added by the programmer to help clarify a given translatable string. this command is not a native NVDA command, but it is defined in the Poedit app module to perform this function.

Typically, an add-on which accepts scripts will have a list of command:function map somewhere in the module. The simplest is a gestures (commands) dictionary, a python dictionary (typically named __gestures) which holds commands as keys and scripts as values for these keys (more than one key, or command can be bound to scripts). These dictionaries are loaded when add-on loads and is cleared when either NVDA exits or the app for the app module loses focus (that is, the user has switched to another program).

Another way to bind scripts is via runtime insertion. This is done by creating another gestures dictionary apart from __gestures dictionary which holds context-sensitive gestures such as manipulating a single control. Then the developer would use inputCore.bindGesture (or inputCore.bindGestures if more than one gestures/scripts are defined) to define certain gestures for a time, then using inputCore.clearGestures then inputCore.bindGestures(__gestures) to remove the added gestures. A more elegant way, which involves scripts for specific objects, will be covered when we talk about app modules and assigning gestures to specific parts of a program.

In a similar manner to scripts, the UI module allows you to say or braille what your add-on is doing. This is done by using `ui.message(something to say)` where `something to say` is replaced by a string for NVDA to say. Alternativley, you can call speech and braille handler methods directly if you want speech to say one thing and the braille display to show something else. We'll not go over `ui.message` here (you'll see examples of those), but what's more important is scripts, so we'll focus on that in this section.

As of time of writing, NVDA supports input from the keyboard, braille displays with or without braille keyboard and touchscreens. These input types have a corresponding gesture prefix (kb for keyboard, br for braille and ts for touchscreen) which identifies the type of gesture. Output can be sent via speech and/or braille.

### Example 2: A basic script dictionary and message output ###

In this example, we'll define two scripts called "sayHello" and say"GoodBye", then bind them into two separate gestures.

	# An example fragment for script assignment from a global plugin.
	import ui
	
	def script_sayHello(self, gesture):
		ui.message"Hello!")
	
	def script_sayGoodBye(self, gesture):
		ui.message("Good Bye!")
	
	__gestures={
		"kb:control+NVDA+1":"sayHello",
		"kb:Control+NVDA+2":"sayGoodBye"
	}

Now when you press Control+NVDA+1, NVDA will say, "Hello", and when you press Control+NVDA+2, NVDA will say, "Good bye." This is the basic code on receiving commands and sending messages.

### Example 3: Scripts for specific objects ###

As in specialist objects above, scripts can be assigned to certain objects by specifying gestures dictionary for this particular object. Here is an example from an app module which defines scripts for main window of a media player program:

	# Scripts for objects for a program.
	from NVDAObjects.IAccessible import IAccessible
	
	class Player(IAccessible)
		def script_saySongName(self, gesture):
			ui.message(self.songTitle_) #Suppose if that variable has been defined.
	
		__gestures={
			"kb:NVDA+T":"saySongTitle"
		}
	
	# And in the main app module:
	def chooseNVDAObjectOverlayClasses(self, obj, clsList):
		if obj.windowClassName == "PlayerWindow": clsList.insert(0, Player)
	

There is something odd going on with this example: normally, when you press NVDA+T, NVDA says the title of the current window, but in this example, it announces the name of the song instead. This is the result of script lookup (see below) where the script for the current object is run instead of title script from global commands. This is a common way of binding new scripts at runtime.

### Script lookup order and command conflicts ###

As your write add-ons with scripts, you need to remember the following script lookup order when trying to assign commands to scripts:

1. Global plugins.
2. App modules for the currently focused program.
3. NVDA objects we're dealing with.
4. Global commands.

For example, if you assign the command NVDA+Shift+Y to an app module script, NVDA will run that script from that program since no global plugin is using this command. However, if a global plugin which uses that command is installed, the script from the global plugin will be run instead of the app module script. Similarly, from the above example, when using programs other than that media player, NVDA will run a command from the global commands collection when NVDA+T is pressed; but as long as we're using this media player, NVDA+T will announce the name of the song (NVDA objects in app modules takes precedence).

Because of the above rule, one should be careful when defining a script for an add-on. To help you with this, keep the following guidelines handy:

1. First, consult the NVDA commands quick reference to see if the command you wish to use has been defined in global commands. You should try to minimize conflicts with built-in NVDA commands. An exception is commands for app modules where same command may be used differently from one program to another.
2. Read the documentation for add-ons (especially global plugins) to see if any add-on is using this command, and if so, contact the add-on author to come up with an alternate binding.

### Few other remarks on scripts ###

* You can define a script category to show the user where your add-on script will be used (shown in Input Gestures dialog in NVDA 2013.3 or later). There are two ways of doing this: module level via `scriptCategory` attribute from the add-on module, or designating the category for each script via `script_name.category` attribute. It is recommended that you name your script category the same as the add-on name.
* You can define the input help mode message for a script by using `__doc__` attribute. The __doc__ attribute is also used in Input Gestures dialog to show the description for a script.
* If you need to leave one or more scripts unassigned (for example, if a gesture conflicts with a global command), do not include the gesture binding for the script in the gestures dictionary. This helps minimize gesture conflicts and allows users to assign custom gestures for scripts.
* If there are two objects, A and B and if B inherits from A and both contain same command for a script, you can assign "None" to script name in object B (subclass) to bypass a command when dealing with commands from object B. For example, if F10 is defined for both objects and F10 is not used in object B, you can assign object B's F10 command to "None" so F10 can be sent to the operating system. This is implemented in some NVDA core modules and in StationPlaylist Studio add-on.

### Events ###

You can ask NVDA to do something if something happens. For example, you can ask NVDA to say the new name for an object when it's name changes, or say the new item's value when the item gets focused. These conditions, or actions are called events.

When an event occurs, NVDA does the following:

1. Finds out what the event was (for example, a check box gains focus).
2. Performs actions for the event (e.g. says the name and the checked state of this check box).
3. Passes the event down the chain in case other objects may have actions associated with the event.

Depending on where the event is defined, you'll need two or four things when defining an event. If it is declared from the add-on module, the required parts are event name, the add-on module (self), object and next handler in case the object has other events associated with it. If it is defined as part of an object, the name of the event and the object (self) is required.

A typical event routine looks like this:

	def event_eventName(self, obj, nextHandler):
		# Do some action.
		nextHandler()

For object events, use:

	def event_eventName(self):
		# Event routine.

In fact, we have met an actual "event" before: `event_NVDAObject-Init`. This is a special event (one of many events defined in NVDA) fired when NVDA meets a new object and initializes it according to your input (see the section on overriding object properties for more information). Let's meet other events you may see while wriring your add-on.

### Example 4: Announcing the changed name of a control ###

The below code came from one of the add-on app modules.

Below is a routine for an event which tells you the name of some text on the screen when the text changes.

	def event_nameChange(self, obj, nextHandler):
		if obj.windowClassName == "TStaticText": ui.message(obj.name)
		nextHandler()

As you can see, whenever the text object's name changes, NVDA will announce the new name to the user. The "name change" event is one of the many events that you can define custom actions for in your add-on (the complete list is below).

Note: You can define events for any object of your choice, especially controls in a program (where you can define custom actions for events in your app module). If this is the case, you need to make sure that the control meets certain conditions you set, such as name, role and so forth to let NVDA keep an "eye" on that specific object.

### List of possible events ###

This is a list of events you may define custom actions for in your add-on:

* gainFocus: The user has moved the focus to a specific control, or the user has just switched to a program.
* loseFocus: Opposite of gainFocus.
* nameChange: The name of a control has changed (see above for an example).
* valueChange: The value of the control such as text of a field has changed.
* stateChange: Useful to keep track of whether check boxes, buttons and other control's state (checked, selected, etc.) has changed.
* foreground: the object we're interested in has become the foreground window of the program.

### Events within objects ###

The above section described event routines from an add-on's perspective. This is just one way of defining events. The other way is to define events from within objects, and is same as above except that it only takes one argument (self).

### Other components ###

Besides objects, scripts and events, you can add other components in your add-on for working with specific controls. For example, you can use a textInfo module (such as NVDAObjects.NVDAObjectTextInfo) for working with text in edit fields and other controls, or use external modules from third-party developers for specialized tasks such as windows registry access (_winreg) and others. You can also use Python's built-in modules (such as time, functools, etc.) for advanced operations.

If you wish to store settings for your add-on, use ConfigObj to store configuration files and settings.

Finally, you can ask NVDA to perform some routines while the add-on is loading or being terminated. This is done by defining `__init__` and `terminate` method for the add-on. Depending on the plugin type, use:

* For global plugin:
	def __init__(self):
		super(GlobalPlugin, self).__init__()
		# The routine to do when the global plugin loads.
		# Warning! You should always call super method first in order to initialize various foundations correctly.
* For app modules:
	 def __init__(self, *args, **kwargs):
		super(AppModule, self).__init__(*args, **kwargs)
		# What NvDA should do when the app module loads.

* For terminating, regardless of the add-on type:
	def terminate(self):
		# Do something when the add-on terminates.
		# Warning! Never initialize ANY core module such as GUI in terminate method as doing so will prevent NVDA from exiting properly.

### Let's build an add-on ###

Now we have a basic overview of components of add-ons, we're ready to build some simple add-ons. But first, let's go over the actual add-on development process, debugging tips, do's and don'ts and other tips.

### Add-on planning and development tips ###

Over the years, the NVDA community built a number of powerful add-ons for NVDA users. Over the course of these years, the add-on writers gathered some useful tips when it comes to writing your own add-ons. Here are a number of them:

* Get to know NVDA: it is important that you become familiar with NVDA commands, concepts and tips. Subscribe to NVDA users groups to learn more about NVDA and learn about how NVDA works, as you'll be extending it via your add-ons.
* Get to know the product at hand: as noted earlier, it is important that you get to know the software you're writing the app module for, synthesizers and braille displays you'll be writing the driver for and so on.
* Plan ahead: if you know you'll be maintaining your add-on for a number of months or years, it is useful to have a plan and write the add-on code to prepare for future extensions. For example, working on features that you need to implement now, dividing parts of a program to objects and so on.
* Ready to debug and test your add-on: writing your add-on code is just one part of the overall add-on development. The other part is testing and debugging your add-on to make sure that users use your add-on with minimal errors. As you write your add-ons, be sure to test your code regularly.
* Most importantly, have fun.

### Do's and don'ts ###

Here are a few things you should do and not do throughout add-on development:

1. Do talk with users: it is important to remember that your add-ons will be used by NVDA users around the world, so it is important to keep in touch with your users to gather bug reports and suggestions.
2. Do ask for help if needed: If you're stuck, you can ask other add-on writers anytime for solutions or tips, or if you need to, ask for colaboration from other add-on developers.
3. Do test your add-on on more than one computer: sometimes, a bug in one computer may help you solve problems on your add-on on your computer later.
4. Don't use fancy code without understanding your intentions: a typo or forgotten indentation can become troublesome when you debug an add-on.
5. Do keep up to date with NVDA core changes: sometimes, you may find that your add-on might not work due to NVDA core code changes. Be sure to read "changes for developers" section in NVDA's What's New document to keep up to date with code changes that may affect your add-on.

### Frequently Asked Questions about add-on components and development ###

Q. When I try to obtain an object using an index, it fetches an object one after the index I wrote.  
This is the side effect of zero-based indexing (count starts at 0).

Q. When importing a module, NVDA says it cannot locate the module.  
Did you type the correct name of the module? Did you extract the module files in the correct location? Try fixing the typo, look at the import path and try importing again.

Q. What is difference between simple review and normal review and which one should I use?  
Simple review excludes layout objects such as windows, grouping and so on which are placed for layout purposes. Normal review includes these as well. The choice of using simple review versus normal review depends on your situation.

Q. The command for my app module does not work in my app module; instead, NVDA does something else.  
Check if a global plugin which uses the command is installed. First, remove the global plugin and try again.

Q. How can I use Win32 API in my add-on or object?
There is a document written by an add-on developer which talks about using Win32 API in your add-on. Select [this link][4] to view this document.

Q. How can I create dialogs in my add-on?
You need to import two modules: GUI (import gui) and WXPython (import wx).

Q. Can I create functions and assign variables outside the module classes?
Yes. This is useful if you need to reference them from inside the add-on class. For example, you may have a function that's defined outside your class that you'll need to use from more than one method in a global plugin class.

Q. I want to save user settings for my add-on. Can this be done?
Yes. You'll need to use ConfigObj library (configObj) to manage configuration. Some add-ons (such as OCR) which uses configuration files store their configuration as an ini file in NVDA's user configuration folder. For global plugins, you can load and save user configuration from the add-on when the add-on is created (__init__) or finished (terminate), respectively. You cannot do this easily with app modules. Also, you'll need to provide a facility (commands, dialogs, etc.) where users can configure add-on settings.

Q. I have a script which calls a function that runs for a long time, and I cannot run NVDA commands when my script runs.
One way to fix this is using threads (separate, independent  operations in a program) via Python's threading module. In order to do this, create a method which you know will run for a long time, then from the script which calls this method, create a new thread (see Python's threading module documentation) that'll be in charge of running this method. This way other NVDA commands can be performed while the add-on method does its work (see Google Speech Recognition module for an example code).

Q. I would like to port a module written in Python 3 syntax for use as an NVDA add-on.
This cannot be done easily. One handy module for this purpose is six, which allows running Python 2 and 3 code. NvDA itself uses Python 2 as WXPython uses Python 2, and until WXPython is rewritten to support Python 3, you cannot run Python 3 code in NVDA.

We did not include programming or Python-related FAQ's, as there are sites which answers questions about Python such as coding style. Consult these documents if you have issues with Python code.

Now that we have covered basic add-on components, let's learn about how to package what you know in your add-on modules themselves: global plugins, app modules and drivers.

## Introduction to global plugins ##

A global plugin adds features available everywhere. For example, if there is a control that will be used in many applications, then you can write a global plugin to handle them throughout NVDA. Another example is adding additional features to NVDA that can be used in all programs, such as OCR capability, place marker management and so on.

A global plugin is a Python source code (.py) file with the name of your plugin. For example, if you're adding support for rich edit fields in many applications, you can name your plugin as richEditSupport.py. When naming them, try be brief so you can see what your plugin does.

### Typical development plan for global plugins ###

Typically, a global plugin is developed thus:

1. You or someone suggests a feature or support for a particular control across different programs.
2. You plan your global plugin (see the section on when to write or not write global plugins).
3. You write your global plugin and test it. Once it is done and tested, you release the plugin.

Since global plugins are Python files, you can use the full power of python in your add-on code. Also, since global plugins have access to full power of NVDA code such as events, scripts and objects, you can use the concepts you learned from previous sections.

### The global plugin code ###

As shown earlier, the procedure for writing global plugins is same as writing any Python program, except that you import globalPluginHandler and put your add-on code in a class called `GlobalPlugin` which inherits from `globalPluginHandler.GlobalPlugin` (see the example in the firse intro chapter). If you need to use third-party modules, you need to place the package in the same folder as the global plugin file and import the external module(s). Then define objects (usually overlay objects), methods and so on in your code.

### When to write or not write global plugins ###

Since global plugins are used everywhere, you might be tempted to write support for a single application using global plugin alone. However, this is not the case. There are other guidelines to keep in mind when deciding whether to write a global plugin or not:

You might consider writing a global plugin if:

1. You or a user wishes to use a certain feature everywhere.
2. You need to support same controls across different applications, provided that the control behaves the same in these programs.

You should not write a global plugin if:

1. If you wish to enhance support for a single application.
2. You are writing support for speech synthesizers or braille displays.

### Few more things to remember about global plugins ###

* When you write scripts in your global plugin, the commands you assign to them will take precedence (looked up first). Therefore it is important to consult the NVDA user guide and help for other add-ons to minimize command conflicts.
* Each global plugin must be placed in globalPlugins directory in your add-on folder structure.
* It is possible to use more than one Python file in your global plugin. If this is the case, you need to put them in a folder (name must be the name of the plugin) inside globalPlugins folder, with the main plugin file named __init__.py.
* If you need to do something when the global plugin is loaded (such as loading the user configuration), you need to write an __init__ method in your plugin class. In this method, you need to call the __init__ method in the super (globalPluginHandler.GlobalPlugin) first before doing other startup work. Also, if you need to do something when the global plugin ends, define terminate method.

Let's go through some examples and exercises.

### Example 1: Writing computer braille using QWERTY keyboard ###

You are meeting with a client who uses Duxbury braille translator (a popular braille document production program). This client is working with another user of NVDA who wishes to write computer braille from his computer keyboard everywhere. Based on this, you decide to write a global plugin, and found a module that allows the computer keyboard to act like a braille keyboard using a function.

The global plugin, named brailleWrite.py, would look like this:

	# An example global plugin.
	
	import qtbrl # The braille entry module.
	import globalPluginHandler
	
	class GlobalPlugin(globalPluginHandler.GlobalPlugin):
		brlentry = False # Braille entry is not active.
		
		def script_toggleBrailleEntry(self, gesture):
			self.brlentry = True if not self.brlentry else False # Toggle braille entry mode.
		script_toggleBrailleEntry.__doc__="Toggles braille entry on or off."
		__gestures={
			"kb:NVDA+X":"toggleBrailleEntry"
		}

With this background in mind, try some of the short exercises below.

### Exercises ###

1. Write a global plugin named nvdaVersion.py to say the current NVDA version when NVDA+Shift+V is pressed.
2. A user wants to hear the time announced every minute. Using the clock on the system tray, write a global plugin to announce when the time changes (hint: you need to use an event and check the role of the clock object).

## Introduction to app modules ##

An app module enhances support for a particular program. For example, you can write an app module which adds convenience commands for reading various parts of the screen, or you can define how a particular control should behave in a program.

An app module is a Python (.py) file with the name corresponding to the executable name of a program. For example, an app module for Winamp is named winamp.py since Winamp's executable name is winamp.exe.

NVDA itself comes with several app modules, such as Winamp, Adobe Reader, Microsoft Office programs and so on.

### Differences between app modules and global plugins ###

At first glance, app modules may look the same as any global plugin. However, app modules have additional properties that global plugins lack, including:

* Instead of `globalPluginHandler`, you need to import `appModuleHandler`. The class to implement is `AppModule(appModuleHandler.AppModule)`.
* App modules are stored in appModules folder in your add-on directory structure and is named the same as the executable name of the program.
* You can ask NVDA to enter sleep mode in a program where NVDA will not speak or braille anything while using the program, and any keyboard commands you press will be handled by the program directly. This is done by setting `sleepMode` attribute in the AppModule class to True.
* The `event_NVDAObject_init` routine is only available in app modules.
* You can ask NVDA to keep an eye on an object to handle events for them even if the user is using another app.

### App module development process and strategies ###

A typical app module is developed thus:

1. You or a user requests enhanced support for a program.
2. If possible, contact the app vendor (programmer) to ask accessibility improvements for the program from their end.
3. With or without cooperation from app vendor, you would examine how the program works and areas on the screen that needs to be read out.
4. Write and test the app module (with users) until the app module is ready for release.

As you write app modules, try these tips:

1. Use objects to represent parts of a program. This is done in two steps: define the control for parts of a program via objects (inheriting from some object such as IAccessible), then use `chooseNVDAObjectOverlayClasses` routine to tell NVDA to work with your custom object when working with that control. See overlay classes section for tips.
2. If possible, test your app module using two or more versions of the program to make sure your app module works with those versions.
3. You should not incorporate all desired features in version 1.0 - leave some of them for a future release.

### Example 2: Simple app module in Notepad ###

Suppose you wish to find out which line you're editing in Notepad. Assuming that Notepad will show status bar at all times, you wish to assign a key combination to read the current line number.

The app module for Notepad would look like this:

	# The example app module for Notepad, notepad.py.
	import appModuleHandler
	import api
	import ui
	
	class AppModule(appModuleHandler.AppModule):
		def script_sayLineNumber(self, gesture):
			# Suppose line number is in the form "  ln 1".
			lineNumList = api.getStatusBar().name.split()
			lineNum = lineNumLisst[2]+linenumList[3]
			ui.message(lineNum)
		
		__gestures={
			"kb:NVDA+S":"sayLineNumber"
		}

So whenever you run Notepad, when you press NVDA+S, NVDA will say line number.

### Example 3: Silencing NVDA in Openbook ###

Openbook is a scanning and reading program from Freedom scientific. Since Openbook provides speech, you can tell NVDA to enter sleep mode while Openbook (openbook.exe) is running using the below app module:

	# Silencing NVDA in openbook, openbook.py.
	import appModuleHandler
	
	class AppModule(appModuleHandler.AppModule):
		sleepMode = True

With that single line of code, NVDA will enter sleep mode in that program (you should do this only if the program provides speech and/or braille support on its own).

### Example 4: Announcing control property changes while using another app ###

You can ask NVDA to handle specific events while focused on another app. This is done by calling eventHandler.requestEvents in app module's __init__ method. In order to invoke this, you need process ID (PID) for the application, window class name for the object and the name of the event to be handled.

The below code allows NVDA to announce value changes while focused on another application.

	# The example app module for a messenger app.
	# The object we wish to track has window class name of "MessengerWindow".
	
	import appModuleHandler
	import eventHandler
	
	class AppModule(appModuleHandler.AppModule):
		def __init__(self, *args, **kwargs):
			super(AppModule, self).__init__(*args, **kwards)
			eventHandler.requestEvents(self.processID, "MessengerWindow", "valueChange")

Once defined, even if focused in another app, new messages (values) will be announced.

### Useful app module properties and methods ###

`sleepMode` and `processID` are just two of many attributes that app modules have. Other useful properties and methods used in app modules include the following:

* appName: the name of the app (usually the name of the executable).
* productName: Records the actual product name for the app.
* productVersion: Records the version of the app.
* is64BitProcess: if true, the app is a 64-bit process (only true if you're using a 64-bit app under 64-bit Windows versions).

And other properties. Type dir(obj.appModule) from Python Console for the complete list.

### Other remarks on app modules ###

Here are other remarks regarding app modules:

* If you find that different versions of the program are laid out differently e.g. locations for controls are different, then you can write code which can handle these cases. There are a number of options you can choose from: adding some constants in your app module to handle different object locations, writing code for these controls (one per version) in custom objects which will be chosen in overlay class method and so on.
* If possible, try working with services that the app provides, such as COM (Component Object Model) methods (for example, Outlook app module), API's the app provides (such as Winamp) and so on.
* To support an application that works the same as another program (especially if you're writing app module for a 64-bit version of a 32-bit program for which you wrote an app module for), use the following code fragment:  
	from appName import *
where appName is the name of the app module and * (asterisk or star) means import everything. For an example of this, look at NVDA's app modules for Miranda32 and Miranda64.
* If you wish to extend an app module that comes with NVDA, use the following code fragment:  
	from nvdaBuiltin.appModules.appName import *
Where appName is the app module you wish to extend. For example, if you wish to support different controls in Windows calculator (calc.py), use:  
	from nvdaBuiltin.appModules.calc import *
* Many app modules (both built-in and third-party ones) uses app names as part of the name for a constant (a value that doesn't change). For example, in NVDA's Powerpoint module (powerpnt.py), many constants starts with "PP". Similarly, in Station Playlist Studio app module, many constants in the app module file (splstudio.py) starts with "SPL". This is used to remind you where this constants are used.

## Drivers

A driver allows a software such as NvDA to communicate with hardware or use functionality provided by another software. Typically, when people speak of drivers, they usually refer to a program installed on a computer that allows software to communicate with a specific hardware, such as video cards, keyboards and so on.

In NVDA, drivers refer to modules that NVDA can use to communicate with a speech synthesizer or a braille display. For instance, you can write a braille display driver that sends braille output to your braille display, or ask your synthesizer to switch languages and provide configurable settings.

### Few important things to remember before, during and after driver development

* Before writing a driver, make sure you have the needed software and/or hardware.
* Be sure to study protocols and API's used by a speech synthesizer or a braille display (this is more so for braille displays which may implement different protocols).
* Make sure you know how to communicate with your equipment - ports, USB ID's, Bluetooth addresses, serial port settings and so on.
* Work with another person who happens to use the equipment or software you are writing driver(s) for.

### Typical driver development steps

When writing drivers, you may wish to follow the recommended steps for app module development (planning, talking to vendors, user test, etc.). However, since drivers require intimate knowledge of hardware and/or software, you should spend more time on testing your driver. This is more so if you are writing a driver for a braille display which can send arbitrary commands (braille commands, routing buttons, etc.).

### Braille display drivers




## Sharing your add-on and experience with others ##

Once you've finished developing your add-ons, you might want to share your code with others. Along the way, you might contribute your know-how so others may benefit from your experiences.

This chapter is designed to give some guidance on add-on release and maintenance, as well as connecting with your add-on users and other NVDA core and add-on developers around the world.

### The NVDA Add-ons list ###

If you want to keep in touch with your add-on users or want to learn from or contribute your add-on to others, please subscribe to [NVDA add-ons list][4]. This is a low traffic list devoted to discussing current and future add-ons, as well as to review other add-ons created by members of the community or have your add-on reviewed by other add-on developers around the world.

### The NVDA Community Add-ons website and code repository ###

To download or learn more about various add-ons created by NVDA users, visit [NVDA Community Add-ons website][5]. You can browse currently available add-ons, view add-ons under development and read add-on development guidelines.

For developers seeking to explore actual code which powers add-ons, you can visit [NVDA Community Add-ons code repository][6] hosted at Bitbucket. To contribute your code, you may either create a Bitbucket acount, then ask on the NVDA Add-ons list to be invited to the NVDA Add-on Team or send an email to the maintainer of the add-on you wish to contribute. NVDA Add-on Team uses [Git][7] for version control.

## Miscellaneous information ##

Please add additional material to this guide. We at NVDA Add-on Team welcome contributions from other add-on developers and users around the world.






# Future sections #

Please delete this notice when appropriate sections are done.

## Add-on components and development tips ##

Includes introductions to input and scripts, output systems, objects, events, configuration, add-on settings and reloading plug-ins. Also includes some tips on add-on development such as debugging. It concludes with some useful examples and do's and don'ts.

Planned sections (please feel free to contribute your knowledge in this section):

* Events and list of available events (not quite done).
* Script lookup process and conflicts (done, may need additional tips from others).
* Debugging add-ons.
* If something goes wrong (common errors and exceptions).
* Few working and non-working examples for each topic.
* These plan sections may change.

## Global Plugins ##

A chapter devoted to global plugins.

Planned sections:

* A few worked out examples (few more needed).
* These sections may change.

## App Modules ##

A chapter devoted to app modules.

Planned sections:

* How app developers can help NVDA users through accessible app designs (not written yet).
* A few worked out examples and examples from existing app modules from NVDA core and from community (more examples needed).
* These topics may change.

## Drivers ##

A chapter devoted to driver development.



[1]: http://community.nvda-project.org/wiki/Development
[2]: http://community.nvda-project.org/documentation/developerGuide.html
[3]: https://bitbucket.org/nvdaaddonteam/addontemplate/get/master.zip
[4]: http://www.zlotowicz.pl/nvda/winapi.mdwn
