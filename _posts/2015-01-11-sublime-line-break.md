---
layout: post
title: Sublime Line Break Plugin 
---

#### Introduction 
This is a sublime  plugin to copy multi line data from a selection to clipboard. There is a sublime shortcut (`ctrl+j`) which do the same thing but do that in-place (changes the contents of file). This plugin copies the content but does not change the actual file.

for example

selection: 

```sql
CREATE TABLE STATION 
	(ID INTEGER PRIMARY KEY, 
	CITY CHAR(20), 
	STATE CHAR(2), 
	LAT_N REAL, 
	LONG_W REAL);
```

copied to clipboard:

```sql
CREATE TABLE STATION (ID INTEGER PRIMARY KEY, CITY CHAR(20), STATE CHAR(2), LAT_N REAL, LONG_W REAL);
```

#### Installation
-  create new sublime pulgin with above code and save it in 
 
 - OS X `~/Library/Application Support/Sublime Text 3/Packages/User/`

 - Linux `~/.config/sublime-text-3/Packages/User/` 
 
 - Windows `%APPDATA%/Roaming/Sublime Text 3/Packages/User/`

-  in user key map add the following key binding

```
	{ 
		"keys": ["ctrl+alt+d"],
		"command": "line"
	}
```


#### Plugin code 
```python 
import sublime, sublime_plugin

class LineCommand(sublime_plugin.TextCommand):
	def run(self, edit):
		s = self.view.sel()
		for region in s:
			if not region.empty():

				selection = self.view.substr(region)
				selection_mod = selection.replace('\n', ' ').replace('\r', '')
				selection_mod = " ".join(selection_mod.split())
			
				#self.view.insert(edit, 0, selection_mod)	
				sublime.set_clipboard(selection_mod)
				sublime.status_message("Selection with removed 'New Line Chars' copied to clipboard")
				#sublime.message_dialog("\"" + selection_mod +"\"" + "copied to clipboard")
```


