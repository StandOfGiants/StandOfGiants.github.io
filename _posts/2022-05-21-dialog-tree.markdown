---
layout: single
title: "How to write a dialog tree"
date: 2022-05-21 12:58:02 -0700
categories: NavyLovin
author_profile: false
toc: true
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

### User Data

When starting up the game, we are planning on having a prompt that asks the users things like their character name, pronouns, hair color, etc.

This data will be able to be inserted into the text using backticks. For example `` `name` `` would be replaced with the user name that was provided by the player.

## Dialog Lines

By default, dialog lines are shown to the player one line at a time. The types of lines are:

### A message said by a character

This is a message that is said by a user. The format of the line is the character's name, followed by a colon (`:`) followed by the message the character says. For example:

```
character: message
```

Would be rendered like:

![char msg](/assets/images/char-msg.png)

### Sections

Sections are extremely important for structuring dialog trees as they allow portions of the conversation to be accessible from multiple places, or for the same section of conversation to be repeated.

For example, if you had a group of responses that you want to be presented to the user over and over, you could do something like this:

```
:: questions
- Question 1
- Question 2
- Question 3
=> questions
```

In this above example, the first line `:: questions` defines the start of a section with the name `questions`. The next 3 lines are responses (which are described below), and the last line `=> questions` (also known as a redirect or a goto) specifies that the conversation should continue at the top of the section named `questions`. In this case, the three questions would be presented over and over again forever. If you wanted to go to a different section on some responses you could do it like:

```
:: questions
- Question 1
- Question 2
- Exit Questions => some_other_section
=> questions
```

In this case, when the user selects the option `Exit Questions` they will be sent to the section named `some_other_section` _instead_ of being sent to the section named `questions`.

All sections should have a redirect as their next line that leads to the next portion of the conversation.

### Responses

A list of responses that will be selectable by the player. All responses start with a hyphen (`-`) followed by the text that is displayed to the user.

#### Redirecting

If you want to redirect to another dialog section when a user selects a response you can use:

`- Text of the response => redirect`

If you want lines to be run after a response is selected you can indent the text:

```
character: question
- Text of the response
    character: response to dialog
- A different response
character: says either way
```

In this case, the line `character: response to the dialog` will only be run if the user selected the `Text of the response` line, and in either case, the line `character: says either way` will be run. You can see that below:

![questions_1](/assets/images/questions-1.gif)

#### Multiple Responses

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

#### Nesting responses

If you want a series of questions and responses to follow each other, you can nest questions beneath each other:

```
- Response A
    Reply to Response A
    - Response A.A
    - Response A.B
- Response B
```

When the user selects `Response A` the line `Reply to Response A` will be run, followed by presenting the responses `Response A.A` and `Response A.B`.

The indented portion of text following a response can be thought of as a new section. For example, the following:

```
- Response A
    Reply to Response A
    - Response A.A
    - Response A.B
- Response B
After responses
```

is the same as:

```
- Response A => after_response_a
- Response B
=> after_responses

:: after_response_a
Reply to Response A
- Response A.A
- Response A.B
=> after_responses

:: after_responses
After responses
```

### Do commands

> Note: It is not strictly necessary for writers to fully understand this section, but it is useful to be aware of what kinds of commands can be run that affect the scene other than just the text.

A line that starts with `do ` is not displayed to the user, but runs some kind of command in the game. You can use it to perform non-dialog actions such as changing backgrounds, or showing a character sprite.

The current existing do lines are:

- `do set_background("filename", Show)` - swap to the background named `filename.png`
- `do set_background("filename", Fade)` - crossfade to the background named `filename.png`
- `do show_character("character", Show)` - show the character image named `character.png`
- `do show_character("character", Fade)` - fade in the character image named `character.png`
- `do hide_character(Hide)` - immediately hide the currently shown character
- `do hide_character(Fade)` - fade out the currently shown character

### A plain message

If you write a line that doesn't match any of the lines described above, it will be displayed without any extra information.

For example the line

```
Message without character
```

will be displayed as:

![without-character](/assets/images/without-character.png)

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
