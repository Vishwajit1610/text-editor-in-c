In the last Step, we have implemented our program in such a way and observed that -> when a key is pressed with `Ctrl` it will map bytes from `1-26` to `a-z` characters. 

We can use this `Ctrl + key` feature to perform some shortcuts. 

First thing we will do is, remove the behavior of quitting the program using just `Q` key and make it `Ctrl + Q` for program termination.

---
To implement this feature, we use the `CTRL_KEY()` function. `CTRL_KEY()` clears the upper 3 bits of the character, leaving only the lower 5 bits.

So, when we set `((k) & 0x1f)` inside the code, `0x1f` is hexadecimal for `00011111`.

And, doing this maps a printable character to the same control character value that of the terminal generates when `Ctrl` key is being held.

This works because `ASCII` encode control characters using the lower 5 bits of the corresponding letter.

---
#### Code Snippet:

```C
/*** includes ***/

#include <ctype.h>
#include <errno.h>
#include <stdio.h>
#include <stdlib.h>
#include <termios.h>
#include <unistd.h>

/*** defines ***/

#define CTRL_KEY(k) ((k) & 0x1f)

/*** data ***/
/*** terminal ***/ 
/*** init ***/

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
        if (c == CTRL_KEY('q')) break; 
	}
	
	return 0;
}
```

---
Now the the program will not terminate by pressing `Q` rather, you will have to press `Ctrl + Q` to terminate the program.

---
Next we will refactor the keyboard input and stop printing the letter when a key is pressed.
[Refactor the Keyboard Input](../03-raw-input-and-output/02-refactor-keyboard-input.md)

---
