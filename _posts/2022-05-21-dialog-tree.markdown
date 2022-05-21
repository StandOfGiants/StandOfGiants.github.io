---
layout: single
title: "How to write a dialog tree"
date: 2022-05-21 12:58:02 -0700
categories: NavyLovin
---

The dialog for this game is designed to be fairly easy to read and write.

## The basics

The dialog system supports several methods of text formatting:

- `*italic text*` becomes ![italic](/assets/images/italic.png)
- `**bold text**` becomes ![bold](/assets/images/bold.png)
- `***bold and italic text***` becomes ![bold-italic](/assets/images/bold-italic.png)
- `~waving text~` becomes ![wave](/assets/images/waving.gif)
- `#shaking text#` becomes ![shaking](/assets/images/shaking.gif)
- `&&sparkling text&&` becomes ![sparkling](/assets/images/sparkling.gif)

it also supports inserting user-specified data into text, for example, `` `name` `` would be replaced with the user provided player name.

> Note: there is currently only support for `` `name` ``, but there may be ways to insert chosen pronouns, eye color, hair color, etc.

## Line types

There are 4 types of line to be aware of:

### `character: message`

This line type is treated as though a character is saying something. It would be rendered like:

![char msg](/assets/images/char-msg.png)

### `do <command>`

This line is a non-dialog action to be performed such as changing backgrounds, or showing a character sprite.

The current existing do lines are:

- `do set_background("filename", Show)` - swap to the background named `filename.png`
- `do set_background("filename", Fade)` - crossfade to the background named `filename.png`
- `do show_character("character", Show)` - show the character image named `character.png`
- `do show_character("character", Fade)` - fade in the character image named `character.png`
- `do hide_character(Hide)` - immediately hide the currently shown character
- `do hide_character(Fade)` - fade out the currently shown character

### `:: section_name` and `=> section_name`

- `:: section_name` describes the start of a dialog section with the name `section_name`
- `=> section_name` denotes a redirect that will move the dialog to the start of the section named `section_name`

### Responses

A list of responses that will be selectable by the player. All responses start with a hyphen (`-`) followed by the text that is displayed to the user.

If you want to redirect to another dialog section when a user selects a response you can use:

`- Text of the response => redirect`

If you want lines to be run after a response is selected you can indent the text:

```
- Text of the response
    character: response to the dialog
```

In this case, the line `character: response to the dialog` will only be run if the user selected the `Text of the response` line.

You can have several response sections following each other like this:

```
- Response A
- Response B
    Reaction to Response B
- Response C => section_after_c
```

this will present 3 dialog options to the user `Response A`, `Response B`, and `Response C` with the following behavior:

- `Response A` has no special reaction, and will continue the dialog after the last response
- `Response B` will run the line `Reaction to Response B` and then continue after the last response
- `Response C` will redirect to the section named `section_after_c`

If you want a series of questions and responses to follow each other, you can nest questions beneath each other:

```
- Response A
    Reply to Response A
    - Response A.A
    - Response A.B
- Response B
```

When the user selects `Response A` the line `Reply to Response A` will be run, followed by presenting the responses `Response A.A` and `Response A.B`.

## Complete Example

With the dialog:

```
:: main
do set_background("mountains", Show)
do show_character("flowey", Fade)
Flowey: Hello there `name` I'm Flowey!
Flowey: Text that ~waves~ text that #shakes#. Here's some &&sparkles&&.
Flowey: What do you want to see?
=> demo

:: demo
- Show me waves!
    Flowey: ~here's your waves~
- Show me the shake!
    Flowey: **#SHAKEDOWN#**
- Italics?
    Flowey: *This text is italicized*
- Bold Italic...
    Flowey: ***Both Bold and ITALIC***
=> demo
```

The dialog interaction would look like:

![demo](/assets/images/demo.gif)
