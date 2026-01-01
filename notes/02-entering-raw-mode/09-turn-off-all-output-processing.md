## Turn off all Output Processing:

By default, the terminal driver may translate newline (`\n`) into carriage-return + newline (`\r\n`) when output post-processing (`OPOST`) is enabled.

The newline `\n` is used to get the cursor on the next line and carriage return `\r` moves the cursor to the beginning of the current line. 

So to turn this off and make our terminal deterministic and remove the "generosity" of the modern terminal - 
We turn of the `OPOST` flag which essentially is a Output flag represented by the 'O' at the start of the name. Similar to that of input flags having 'I' as the starting character. This `OPOST` flag also comes from `<termios.h>` library. 

---

Next when we DO OFF the `OPOST` flag, the article says, *"If you run the program now, you’ll see that the newline characters we’re printing are only moving the cursor down, and not to the left side of the screen."* 

Which I HAVE experienced, your cursor just doesn't go at the beginning of the next line and keeps extending to the right when newline is pressed.

To fix this, we add carriage return `\r` before our newlines `\n` while using `printf()` function , which will first put our cursor to the beginning of the current line and then newline is executed.

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
	raw.c_iflag &= ~(IXON | ICRNL);
	raw.c_oflag &= ~(OPOST); // Disable OPOST Flag.
	raw.c_lflag &= ~(ECHO | ICANON | ISIG | IEXTEN);

	tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}

int main() {
	enableRawMode();

	char c;
	while (read(STDIN_FILENO, &c, 1) == 1 && c != 'q') {
		if (iscntrl(c)) {
			printf("%d\r\n", c); // Adding carriage return.
		} else {
			printf("%d ('%c')\r\n", c, c); // Adding carriage return.
		}
	}
	
	return 0;
}
```

---
From now on, whenever we have to start a fresh line we have to write out both carriage return `\r` + newlines `\n` in the `printf()` function.

---
## Turn off Miscellaneous Flags:

Now let us turn off come more flags. These flags are pretty much already turned off by default on modern Terminals, but still it is considered a good practice (by someone) to turn off these flags and to achieve RAW mode.

Up till now, every flag disabled had an observable effect on our terminal, but the flags we are about to turn off now are the behavior which we don't want it to exists at all. And by default it is off in modern Terminals.

We turn of the following flags:

1. `BRKINT` - Break condition (historically from serial lines) will cause the `SIGINT` signal to be sent to the program. Editors disable this to ensure no input can unexpectedly become a signal.

2. `INPCK` - Enables parity checking on incoming bytes, which we don't require. 

3. `ISTRIP` - Forces every input byte to be 7 bits by stripping out the 8th bit. This is probably turned off by default.

4. `CS8` - Is NOT a flag but a mask, and it sets characters size to 8 bits. It is a value we select, not a flag we disable. Therefore we use `|=` rather than `&= ~`. 

	To understand the `CS8` even more, `CSIZE` is a filed having multiple **mutually exclusive values inside it,** such as `CS5` `CS6`, `CS7` and `CS8`.
	 And we can only enable one at a time. Which we do by `raw.c_cflag |= CS8;`. 


All these flags comes from `<termios.h>` library. These flags are largely irrelevant on modern terminal emulators, but are disabled to enforce a clean, portable, 8-bit-clean raw input stream.

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
	raw.c_iflag &= ~(IXON | ICRNL | BRKINT | INPCK |ISTRIP); // Disable Flags.
	raw.c_oflag &= ~(OPOST);
	raw.c_cflag |= (CS8); // Select Mask.
	raw.c_lflag &= ~(ECHO | ICANON | ISIG | IEXTEN);

	tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}

int main() {...}
```

---

Next we will set a timeout to our editor: 
[A Timeout for read()](../02-entering-raw-mode/10-timeout-for-read().md)
