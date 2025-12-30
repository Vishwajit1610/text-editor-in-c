`Ctrl + S` and `Ctrl + Q` are used for **Software Flow Control**, which uses special codes **such as XOFF and XON** ("transmit off" and "transmit on" respectively).

`Ctrl + S` stops the data from flowing to the terminal until you press `Ctrl + Q`. This is used when you want to pause a process and let the other device like a printer catch up. 

`XOFF` to pause transmission and `XON` to resume transmission.

We don't need it, so we disable it.

---
Essentially, we will disable the `IXON` flag which comes from `<termios.h>` library and IS A input flag, unlike the previous flags which we worked on. 

---
#### Code Snippet:

```C
#include <ctype.h>
#include <stdio.h>
#include <stdlib.h>
#include <termios.h>
#include <unistd.h>

struct termios orig_termios;

void disableRawMode() {...}

void enableRawMode() {

	tcgetattr(STDIN_FILENO, &orig_termios);
	atexit(disableRawMode);

	struct termios raw = orig_termios;
	raw.c_iflag &= ~(IXON); // Turn off IXON 'input' Flag.
	raw.c_lflag &= ~(ECHO | ICANON | ISIG);

	tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}

int main() {...}
```

---

When implemented, now `Ctrl + S` will be read as 19 byte and `Ctrl + Q` as 17 byte.

And everything else with it works fine, EXCEPT the article says:

- *When you press `Ctrl + V`, on some systems you might face a waiting time in which terminal waits for you to type another character and then sends that character directly.*
  This personally on my Linux System (Arch btw), I did not face this issue. BUT STILL we will do the implementation of turning off the corresponding flag which causes this.

- When pressing every letter from a - z with `Ctrl`, we are supposed to get the values from 1 - 26 but when you press `Ctrl + M` you will get value as 10, even though `Ctrl + M` value should be 13. Just like `Ctrl + A` value is 1.

In the next Step we disable `Ctrl + V` and fix `Ctrl + M` behavior:
[Disable Ctrl + V and Fix Ctrl + M](../02-entering-raw-mode/08-disable-ctrl-v-and-fix-ctrl-m.md)

---
