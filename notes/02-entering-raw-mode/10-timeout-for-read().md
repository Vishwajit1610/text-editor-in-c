Right now, our **`read()` function waits indefinitely to receive a input from the user.** If we don't want this to happen and like have some animation of it in that waiting period, so that `read()` returns after some waiting time.

We can set it up in this step, by the help of **`VMIN` and `VTIME`, they are the indexes of the `c_cc` field, and comes from `<termios.h>` library.**

---
The **`VMIN` value sets the minimum number of bytes of input required before the `read()` returns.** We set `VMIN = 0` so `read()` is allowed to return even if no bytes are available, subject to the `VTIME` timeout.

Meaning, **with `VMIN = 0` and `VTIME > 0`, `read()` waits up to `VTIME` tenths of a second for input.** If at least one byte arrives during this period, `read()` returns immediately with the number of bytes read; otherwise it returns `0` on timeout.

The **`VTIME` value set's the time between the `read()` return, if we set it as `1` then it will be `100ms`**, for me I will prefer to set it as `10` which is `1sec`.

**If `read()` times out, it will return `0`,** it returns `0` because it's return value is of how many bytes it has read. It will print `0` because `read()` returns `0` AND since we've set `c = '\0';` which is an unchanged buffer.  

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
	raw.c_iflag &= ~(IXON | ICRNL | BRKINT | INPCK |ISTRIP);
	raw.c_oflag &= ~(OPOST);
	raw.c_cflag |= (CS8);
	raw.c_lflag &= ~(ECHO | ICANON | ISIG | IEXTEN);
	raw.c_cc[VMIN] = 0;   // Set the min bytes to be read before read() returns.
    raw.c_cc[VTIME] = 10; // Set the time interval between the returns, 1 second.

	tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}
c
int main() {
	enableRawMode();

	while(1) { // Loop until 'q' is pressed.
        char c = '\0';
        read(STDIN_FILENO, &c, 1);

		if (iscntrl(c)) {
			printf("%d\r\n", c);
		} else {
			printf("%d ('%c')\r\n", c, c);
		}
        if (c == 'q') break;
	}
	
	return 0;
}
```

---

When this is implemented and program is run, you will see `0` being printed when you are not typing anything in the program.

---
At this point, **we have enabled the terminal’s raw input mode suitable for a text editor.**

Next we will implement some error handling:
[Error Handling](../02-entering-raw-mode/11-error-handling.md)

---
