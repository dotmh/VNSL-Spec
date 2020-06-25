Visual Novel Script Language
============================

Introduction
------------
Visual Novel Script Language (VNSL), is a language used to for defining a Visual Novel game's script. It is designed to be used by the game designers to define the script, flow and actions of their game. Its main design goal is to be easy to read and write for non technical people, but still be powerful enough to design reasonably complex visual novel style games without the need for a lot of programming.  

VNSL files are text files that have the extension `.vnsl`, and should be in UTF-8 format. 

Example
--------
```
	Start: Welcome
	
	Character: paul
		- name: Paul
		- asset: assetPaul
		- color: red
	
	Scene: Welcome
	
	# This is a comment
	Welcome to the script 
	
	paul; Welcome to the world of *VNSL* 
	paul; What would you like to know?
		? About VNSL
			- Action: goto SDLAbout
		? About You
			- Action: goto aboutYou
		? Exit
			- Action: system exit

```

Features
--------
### Blocks
At the core of VNSL is the concept of a block. Blocks like in other languages logically group code together. Blocks are such a fundamental concept within VNSL that it doesn't have any special way of been defined. A block simply starts at a new line without any whitespace at the beginning and ends at the next new line without any whitespace at the beginning. To add code into a block you simply start the line with whitespace. 

The VNSL parser reads the code one block at a time. 

_Note: because VNSL is a simple scripting language aimed at a specific use case it has no concept of scope. Therefore unlike some other languages blocks don't effect scope._

#### Example 
```
This is a new block of dialog
	This dialog is contained within the block 
	# so is this comment 
	- Action: mood bob happy 
	so is this line and the above action
bob; However this character dialog is a new block.  
```

### Dialog
Dialog is any text that appears in a VNSL file without been prefixed by a keyword or language symbol. There are two types of Dialog in VNSL files character dialog and narrator dialog. 

#### Character Dialog 
Character dialog is prefixed by a character reference. A character reference is a word followed by a semi-colon (;). The character reference word can ether be a literal name or a character alias. In the case of a literal name the character reference minus the semi-colon will be printed in the dialog box. When a character reference is used the Character data is looked up, and that data is used such as the name. That character is also added to the scene automatically. 

A Character reference is considered a literal name, unless a matching alias was defined by the `:Character` keyword. 

#### Narrator Dialog 
Any dialog written in a VNSL file without a character reference prefix. 

#### Formating
Special characters are used to format text. If you wish to use the literal version of these characters they should be prefixed by a single '\\' 

_The specification tries to avoid using common punctuation to make it easier for the writer._ 

- `_` placing an underscore before and after a word or sentence will make that them _italic_ 
- `*` placing an Asterix before and after a word or sentence will make them **bold** 
- `^` placing a Circumflex Accent aka "Control" before and after a word or sentence will make it superscript 
- `~` placing a Tilda before and after a word or sentence will make it subscript
- `<` placing a Greater Than before a sentence will make it an ordered list
- `>` placing a Less Than before a sentence will make it an unordered list.

#### Multiline Dialog
When writing Dialog, you will often need to write a lot for one character, to make this easier to read and more natural to write VNSL supports multiline dialog. You specify that the new line belongs to the same dialog block by intending it with a single tab. If you want to start a new paragraph within the same dialog block simply add a new line that only contains a single tab. 

##### Example
```
bob; Hello and Welcome
	to my new game this is a multi line
	dialog block 
bob; Where this is a new bit of dialog
```

```
bob; This block contains a paragraph
	
	This text will be displayed as a new paragraph within the same block 
	Where as this text is a new line in the same paragraph. 
```

### Filters
Filters are similar to actions, but they act on dialog. A filter starts with a Pipe (|) and acts on the word before the pipe i.e. `1983|age` will call the filter `age` and pipe the value `1983` to it. The value 1983 will then be replaced by the result of the filter. 

You can pipe more than one word to a filter by surrounding them with a back tick (\`) at the start and end i.e. 

```
`Hello world`|greet
```

#### Example
```
mark; I Was born 1983|age years ago. 
```

### Lists
A lists is a language construct for defining a list of items. This is not the same as the unordered and ordered list formatting options. A list is not treated as dialog but rather as a data. 

A list in VNSL is similar in concept to an Object and an Array in Javascript depending on whether this list has keys or not. List is used to pass data around between actions, and keywords. 

A list is defined by starting a line with a hyphen (-), with each new item on a separate line. All lists are processed by the parser in the same order as they are defined. Items intended with the same amount of white space as the previous line will be grouped into a single list. Items intended further than the previous line will be nested under the previous list item. 

Lists use the concept of a block to determine what the list belongs to, therefore all lists should be contained within a block. The list will then be passed to the top of that block. 

#### Example

##### Example 1
```
Scene: welcome
	- Action: set background bgAsset
	- Action: set music bgMusic
	- Action: set mood ALL sad
```
 
This is an example of a list, that contains 3 items. Each item is an `Action:`. The list is scoped to the Scene so as soon as the player enters the "welcome" scene it will then execute the 3 set actions. 

##### Example 2
```
Character: zoe
	- name: Zoe
	- assets:
		- happy: zoeHappy
		- angry: zoeAngry 
	- color: #FF0000
```
 
This is an example of a list that has keys and another list nested. This list is scoped to the `Character:` keyword. The `assets` key contains a nested list as its value which contains the asset for different moods in this case happy and angry. 
 
### Questions

This is the main form of user interaction, you ask the user what choice they want to make. Questions are defined in VNSL using a `?` question mark character at the start of the line. A question is an extended form of Dialog, so it starts with a standard line of dialog, this is the questions body. 

Under the dialog create a new block with each option on a new line starting with a `?` question mark. These are the answers. Each answer then contains a list of actions that selecting that answer executes. 

#### Example
```
bob; What would you like to do?
	? Eat Dinner
		- Action: goto sceneDinner
	? Go to Bed
		- Action: goto sceneBed 
```

### Comments
Like all good languages VNSL allows you to add comments to your script. Comments are ignored by the VNSL parser. A comment starts with a hash (#) character. 

#### Example
```
# This is a comment 
```

Keywords
--------
A keyword instructs the reader that the information is describing aspects around the script and isn't part of the script itself. Such as Character descriptions or what actions should be taken etc. 

All keywords start with a capital letter, the word is then followed by a colon (:) and finally any parameters. i.e. `Action: goto scene2` 

Some keywords need parameters in order to allow them to best carry out their function. Keywords can have both Parameters that directly follow the Keyword name, and options that appear in a list object under the keyword. When declared Parameters are required, where as options as the name suggests are optional. Options always have to have a name to identify them this is lowercase, again followed by a colon (:) and then followed by the option value i.e `name: Bob`. 

### Character
Defines a character 

#### Parameters
- `Alias`: _String_ the alias to refer to the character in the script must only contain letters, numbers, underscores (_) or a hyphens (-). It must start with a Letter. 

#### Options
- `name:`: _String_ the characters display name
- `color`: _String_ the colour to show the characters name in as a Hex colour code.
- `asset:`: _String|List_ the asset to use for the character can be a single asset , or a list contains mood assets. 

_Other options can be available for the character keyword, this are implemented by the reader and do not form part of the specification_  

### Scene
Defines a scene block

Allows you to group Dialog and Actions together into a scene. A scene starts when the `Scene:` keyword is declared and finishes when ether the `Cut:` keyword is declared or a new `Scene:` keyword is declared. 

#### Parameters
- `Name` : _String_ The name of the scene. Must only contain letters, numbers, underscores (_) or a hyphens (-). It must start with a Letter. This must be unique across your entire script.  

### Cut
*Optionally* Defines the end of a scene block

### Action
Call an action that is carried out when the action conditions are met.

Actions are the second most important aspect of a VNSL file after dialog. The actions are carried out by the Game engine. Available actions are defined by the Game engine, or application processing the VNSL. These do not form part of the VNSL specification only the Action keyword. 

Unlike other keywords Actions are context specific, this means that they get triggered only when they come into context. Whether you want to run a single action or multiple actions, they should always be contained in a list block.

#### Parameters 
- `Action` _String_ The name of the action to call 
- `Params` _Any_ The parameters to pass to the actions

#### Options
Unlike other keyword Action has no options of its own but rather any options that are defined are passed to the action.

#### Example 
```
Scene: Welcome
	- Action: set background asset1
	- Action: set audio asset2
			- autoplay: true
```

This code would make the background `asset1` and play the audio track `asset2` when the player enters the scene `Welcome`

### Macro
Defines a list of actions under a name

There are times when you want to use the same actions over and over for different contexts to do this you can of course type them out again and again. However this can become hard to support, not very flexible and time consuming therefore you can also define a macro.

A macro defines a new context that when called, runs the actions that are defined under that macro. 

#### Parameters
- `Name` : _String_ the name of the macro Must only contain letters, numbers, underscores (_) or a hyphens (-). It must start with a Letter. This must be unique across your entire script.  

#### Example
##### Declaration
```
Macro: city_scene
	- Action: set background city1
	- Action: set audio city_sound1
	- Action: fx smock1
```

This code would create a new macro called `city_scene` when used in the script it would cause the game engine to call the actions `set` and `fx`.

##### Usage
```
Scene: City
	- Action: city_scene
```

This would then call the `city_scene` macro from our declaration example, when the player enters the scene `City`. 

### Start
Defines where the scene name that the script should start been read from 

#### Parameters 
- `Name` : _String_ the name of the scene declared with the `Scene:` keyword where the script should start been read from. 

### Include
Includes another VNSL file. 

Larger script files, with lots of Macros, Character definitions and Scenes may become hard to read. Following other programming languages this is solved by allowing you to include other VNSL files into one. For example you could create  a separate `characters.vnsl` file and a separate `macros.vnsl` file then include them into your main script. This would make your main script more focused and readable. 

#### Parameters
- `Path` : _String_ the relative path to the script file to include, the extension can be omitted.

#### Example 
```
Start: Welcome 

# With the extention
Include: characters.vnsl
# Without the extension
Include: macros

Scene: Welcome 
....
```
