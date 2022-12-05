# GameMaker-To-LDTK
Tool written in gml to import your Game Maker objects settings into LDtk level editor

## About G2L
G2L, or in long GM to LDtk, is a tiny tool written in gml to import your Game Maker objects settings into LDtk level editor to fasten the setup, even on complex projects.

### Use case
Some may prefer LDtk level editor over Game Maker's room editor for its auto-tiling features, ease of navigation in the ‘world’ of rooms and the overall user experience. Also, while you build and modify your levels in LDtk, they can be tested live in Game Maker while your game is running with LDtk_Parser from Evoleo LDtkParser https://github.com/evolutionleo/LDtkParser. Then, if you want your LDtk levels in actual .yy room file within your Game Maker project you can create those files  with LDtk_Room_Builder from Ailwuful LDtk-Room_builder https://github.com/Ailwuful/LDtk-Room_builder. 
The only hurdle that was left for me was the time needed to create all Game Maker’s objects within LDtk when starting designing levels, and in particular the time to configure all variables definitions for objects / entities. This tool does this for you : retrieve Game Maker objects configurations from all your objects and load them in LDtk so that you can start designing level with no migration time.

### Disclaimer
I take no responsibilities if this breaks your .ldtk file. 
It is largely advised for your first test, to save to a new file (instead of to an actual LDtk project) to see how the tool behaves. Then always backup your .ldtk file before using G2L.
G2L does not change your Game Maker project, so at least you are safe on this side.

### License
G2L is fully free. Do whatever you want with it.

### Version and Platform
G2L is tested on Game Maker LTS and on windows platform.
Due to my use of get_open_filename() and get_save_filename() functions, path definition may malfunction on other platform (says Game Maker’s manual). This will only affect the definition of the source Game Maker folder and of the destination LDtk file. In case of issue, you are kindly invited to find other way to define those two paths, by hard coding them in the code or by text input.

## Installation
G2L needs to be imported as a local package in your Game Maker project.
-	Download the .yymp file from GITHUB.
-	Import it inside your project. You can do this by dragging the *. yymp file from an explorer window onto the GameMaker IDEor by clicking "Import Local Package" within the Tools Menu. In both case, a window will pop up to define import parameters. Click “add all” and “OK”. This will create a new folders in your Asset Browser labeled “G2L”. You are all set.
If your LDtk file is out of Game Maker sandbox repository, which will surely be the case, you need to de-activate sandboxing. You can do so, on the Desktop targets (Windows, macOS, and Ubuntu (Linux)), by checking the “Disable file system sandbox” option in the Game Options for the target platform.

## How to use
### Activate
Once imported in your project, you can activate G2L anytime by pressing CTRL+SHIFT+G.

### Define Game Maker project and LDtk file
The first time you run G2L on your project, you will need to define two path so that G2L knows where to scan objects and what destination file to import data into:
-	Click “Select Game Maker directory”, find and click the .yy file of the project you want to scan and export data from (the .yy file is not used, it is the associated directory that is collected).
-	Click “Select destination LDtk file”, find and click the .ldtk file you want to import data into.
The Game Maker directory and the .ldtk destination file are both saved in your user preferences for later uses. Of course, you can update them anytime.
(For now, you can only export from the Game Maker project where you have installed G2L. In the future, I would like to be able to export from any Game Maker project)

### Export to LDtk
Once your Game Maker directory and the destination .ldtk file defined, you can import your data in two steps, as follows:
-	Click the ‘Scan variables from Game Maker’, this will display the “Import window” (see below) for you to adjust parameters before processing the actual import.
-	Once you are happy with your parameters,  click ‘Merge variable into xx.ldtk’.
You are done. Your destination .ldtk file now contains the proper syntax for each entity to match the variables definition from your Game Maker directory

### Merge window details
The Merge window allows you to select what objects and what variables you want defined in the .ldtk file (you can see it as a git merge). 
The Merge window displays all objects from your Game Maker source project and their associated variables (as defined in the variable definition feature of Game Maker objects). For each variable, it displays the name, the type, the default value (which is never undefined in Game Maker) and the accepted range (if defined in Game Maker). You can check/uncheck whole object or specific variables to decide what to import.
Merge preferences are saved in user preferences, to fasten next merges.
It means:
-if you have created new objects since the last merge, they will be listed as to be imported with all their variables.
-if you have modified variables for existing objects since last merge, new variables will be included in the merge, changes in existing variables will be reflected (type, range...).
-changing names of objects or variables will be reflected as a delete of the old entities and creation of new ones.

Some variable types from Game Maker do not exists in LDTK, namely asset type, lists, code. By default, those variables will not be imported by G2L.
Please note that the objects and the variables should have the exact same name in LDtk and in Game Maker to require a merge. Item with different names are considered different can coexist. This name is case sensitive.
Some variable types from Game Maker do not exists in LDTK, namely asset type, lists, code. By default, those variables will not be imported by G2L.


## Behind the hood
G2L is mainly working thanks to:
-Jujuadam’s SNAP library. SNAP offers advanced data parsing features. If you do not know about it, or worse about Juju’s library, you can check them out here: https://github.com/JujuAdams
-Rousr's imguigml. imguigmgl is a wrapper of dearimgui library that allows to build user interface in no time. You can download it here: https://imguigml.rou.sr/

Other than that, the tool proceeds in three steps:
1-a-Scan object .yy files from the Game Maker directory, to extract all data about their variables definition. 
1-b-Arrange this data in JSON format stored in a Staging Area. In the processs, it translates from Game Maker to LDtk syntax (a variable.name in game maker translates to a field.identifier in ldtk for instance, a variable.default_value to a struct { type, range, } etc…
2-Parse the destination LDtk file into an other struct.
3-a-Compare data from the Staging Area (1-b) to data from the LDtk file (2), and display options to choose between the two sources. 
3-b-Update the Staging Area data with he user'choices, to mark what is to be imported or not. 
4-Write back to the LDtk file, the updated struct that takes into account user's choice from 3-b.
