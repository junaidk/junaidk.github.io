---
layout: post
title: Intellij Idea Shortcuts
---

These are some of my most used keyboard short cuts

- Syntax aware selection in the editor selects a word at the caret and then selects expanding areas of the source code. For example, it may select a method name, then the expression that calls this method, then the whole statement, then the containing block, etc.: `Ctrl+W`

- Basic Code Completion, to complete methods, keywords etc.:` Ctrl+Space`

- Go to Declaration. Use this to navigate to the declaration of a class, method or variable used somewhere in the code: `Ctrl+B`

- Introduce Variable Refactoring, to create a variable from an expression. This expression may even be incomplete or contain errors. Since version 8, IDEA intelligently selects a likely expression when no text is selected: `Ctrl+Alt+V`

- Go to Class, to quickly open any class in the editor: `Ctrl+N`

- To open any file, not just classes: `Ctrl+Shift+N`

- Comment/Uncomment current line or selection: `Ctrl+/ and Ctrl+Shift+/`

- Quick JavaDoc Popup to show the JavaDoc of the method or class at the text cursor: `Ctrl+Q` (Ctrl+J on Mac OS X)

- Smart Type Completion to complete an expression with a method call or variable with a type suitable in the current Context: `Ctrl+Shift+Space`

- Rename refactoring to rename any identifier. Can look in comments, text files and across different languages too:` Shift+F6`

- Select in Popup to quickly select the currently edited element (class, file, method or field) in any view (Project View, Structure View or other): `Alt+F1`

- Highlight Usages in File. Position the text cursor on any identifier without selecting any text and it will show all places in the file where that variable, method etc. is used. Use it on a throws, try or catch keyword to show all places where the exception is thrown. Use it on the implements keyword to highlight the methods of the implemented interface:` Ctrl+Shift+F7`

- Add/remove a selection: Alt + Shift + Mouse Click

- Select/unselect the next occurrence: Alt + J / Shift + Alt + J (Ctrl + G / Shift + Ctrl + G) for Mac OS X)

- Select all occurrences: Shift + Ctrl + Alt + J (Ctrl + Cmd + G for Mac OS X)