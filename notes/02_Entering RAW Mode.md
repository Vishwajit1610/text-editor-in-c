
If we are building a Terminal, we've got to understand modes in which the terminal or a text-editor can operate. There are mainly 2 modes but there's also a third mode which is called CBreak (Rare) Mode which we ain't going on in this.

---
In this Step, we essentially understand the difference between the two modes -> 
## Canonical (Cooked) V/S Non-Canonical (RAW) Mode
#### 1. Canonical (Cooked) Mode

- This is the **default input processing method for terminals** in Unix-Like systems, including Linux. 
- In this the **input entered is handled on a line-by-line basis.** Meaning the input is sent to the running program only after the user enters a delimeter which is usually new-line (Enter key), end-of-line (Ctrl+D).
- This let's the user edit using backspace fixing the errors and finally send it to the program by pressing Enter.

#### 2. Non-Canonical (RAW) Mode

- In this mode the **input is processed on a character-by-character basis.** 
- Meaning the characters are sent to the program as soon as they are typed, and not after pressing Enter Key.
- This makes it ideal for real-time application such as games, editor like Vim (I use NeoVim btw). 

**Thing to note: Canonical vs non-canonical is not “editor behavior”, it’s terminal driver behavior.**

---

Now, since the default mode is Canonical, **we want our text-editor to be in RAW mode.**
And the guide says, ***"This can be done by turning off a great many flags in the terminal which we will do gradually over the course of this chapter."***

**RAW mode is not a single switch. It’s a bundle of flags.** And the flags we will turn off along the way.

---
### Turn off Echoing

First thing we do is turn off the `Echo` feature, what it does is -> **when a key is pressed, it prints it to the terminal.** 

This feature is good in Canonical Mode, but we would want to get rid of it while switching to RAW mode. 

When you DO turn this off, it doesn't print what you type and it acts similar to what appears when you are trying to enter password in sudo mode inside Linux.



