Now we will STOP printing the key-presses and implement function for low-level key-press reading, and another function to map the key-presses to editor operations.

---
#### Below we implement:

1. `editorReadKey()` - This function keeps calling read over and over again until it receives a key-press, and ONLY THEN it returns the byte which has been pressed OR terminates the program when there's a real error. ELSE it wait's in case of Timeouts or EAGAIN error.
2. `editorProcessKeypress()` - This function calls the `editorReadKey()` function and get's the return value from it, IF the return value corresponds to `CTRL + Q'` then we terminate the text-editor, and this becomes our termination case.

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

struct termios orig_termios;

void die(const char *s) {...}

/*** terminal ***/

void disableRawMode() {...}

void enableRawMode() {...}

char editorReadKey() {
    int nread;
    char c;

    while ((nread = read(STDIN_FILENO, &c, 1)) != 1) {
        if (nread == -1 && errno != EAGAIN) die("read");
    }
    return c;
}

/*** input ***/

void editorProcessKeypress() {
    char c = editorReadKey();

    switch(c) {
        case CTRL_KEY('q'): exit(0);
    }
}

/*** init ***/

int main() {
	enableRawMode();

	while(1) {
        editorProcessKeypress();
    }
	
	return 0;
}
```

---
Note that, `editorReadKey()` belongs in `/*** terminal ***/`  because it handles low-level terminal input , and `editorProcessKeypress()` belongs in the new `/*** input ***/` because it deals with mapping keys to editor functions at a high level.

We also have segregated each function and built different sections, and now our `main()` is vastly simplified. 

Now if you were to type anything nothing would print, as we would do it in the step coming later on.

In the future we will - 
1. In `editorReadKey()`, handle escape sequences, by reading multiple bytes which represents a single key-press, for example arrow keys.
2. In `editorProcessKeypress()`, map `Ctrl` key to different keys, and other special keys to different editor functions.

---
In the next step we learn to clear the screen:
[Clear the Screen](../03-raw-input-and-output/03-clear-the-screen.md)
