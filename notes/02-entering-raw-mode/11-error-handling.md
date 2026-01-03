Now, **`enableRawMode()` fully helps us get into RAW mode.**

Now we will do some error handling, in which we will be defining a `die()` function which will first print the error message and then exit with an `1` return code.

Then we will call `die()` function for each library calls when they fail. 

---
We will be using `perror()` function which works like: 
When a failure occurs in library function, they set the global `errno` variable to indicate what the error was. And this `perror()` function reads that `errno` variable and prints a descriptive error message about that error. 

After printing the error message, we exit with return code `1`, a non-zero value which indicates an error has occurred.

---
#### Code Snippet 1:

```C
#include <ctype.h>
#include <stdio.h>
#include <stdlib.h>
#include <termios.h>
#include <unistd.h>

struct termios orig_termios;

void die(const char *s) {
    perror(s); // Calling perror().
    exit(1); // Exiting with return code 1.
}

void disableRawMode() {...}

void enableRawMode() {...}

int main() {...}
```

---
Now, we implement this `die()` function for each of our library calls.

#### Code Snippet 2:

```C
#include <ctype.h>
#include <errno.h>
#include <stdio.h>
#include <stdlib.h>
#include <termios.h>
#include <unistd.h>

struct termios orig_termios;

void die(const char *s) {...}

void disableRawMode() {
    if(tcsetattr(STDIN_FILENO, TCSAFLUSH, &orig_termios) == -1)
        die("tcsetattr");
}

void enableRawMode() {

    if(tcgetattr(STDIN_FILENO, &orig_termios) == -1)
        die("tcgetattr");

    atexit(disableRawMode);

	struct termios raw = orig_termios;
	raw.c_iflag &= ~(IXON | ICRNL | BRKINT | INPCK |ISTRIP);
	raw.c_oflag &= ~(OPOST);
	raw.c_cflag |= (CS8);
	raw.c_lflag &= ~(ECHO | ICANON | ISIG | IEXTEN);
    raw.c_cc[VMIN] = 0;
    raw.c_cc[VTIME] = 10;

	if(tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw) == -1)
        die("tcsetattr");
}

int main() {
	enableRawMode();

	while(1) {
        char c = '\0';
        if (read(STDIN_FILENO, &c, 1) == -1 && errno != EAGAIN) die("read");

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
`errno` and `EAGAIN` comes from `<errno.h>`.
Now, `tcsetattr()`, `tcgetattr()` and `read()` all return `-1` when there's a failure and set the `errno` value to indicate the error.

---
And now, Entering-Raw-Mode comes to an end. The last thing we will display the whole code into different section:

#### Code Snippet 3:

```C
/* includes */

#include <ctype.h>
#include <errno.h>
#include <stdio.h>
#include <stdlib.h>
#include <termios.h>
#include <unistd.h>

/* data */

struct termios orig_termios;

/* terminal */

void die(const char *s) {...}

void disableRawMode() {...}

void enableRawMode() {...}

/* init */

int main() {...}
```

---
Next Step:
[Ctrl-Q to Quit](../03-raw-input-and-output/01-ctrl-q-to-quit.md)

---
