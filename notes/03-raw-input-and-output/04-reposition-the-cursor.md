# Reposition the cursor

Right now, when we are entering our text-editor by executing the program, the cursor stays on the same line from which the program was executed.

To fix this, again `write()` and use `\x1b[H` and this escape sequence will be 3 bytes long. The `H` command is for positioning the cursor, and takes two arguments - The row number and the column number. By default it is set to `1` so we keep it that way.

---
#### Code Snippet:
```C
/** output ***/
void editorRefreshScreen() {
    write(STDOUT_FILENO, "\x1b[2J", 4);
    write(STDOUT_FILENO, "\x1b[H", 3);  // Re-positioning the cursor
}
```

---

Now the cursor sits at the start of every program beginning.

---
# Clear the screen on exit

Before rendering the screen with key-presses, we make sure to clear the screen on exit, because if there some rendering problem then we would prefer it to NOT show in out terminal. 

Therefore, we put both the `write()` for clearing the screen and re-positioning the cursor inside `die()` and also when there's termination of program meaning when `CTRL + Q` is pressed.

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
/*** data ***/
/*** terminal ***/

void die(const char *s) {
    write(STDOUT_FILENO, "\x1b[2J", 4);
    write(STDOUT_FILENO, "\x1b[H", 3);

    perror(s);
    exit(1);
}

void disableRawMode() {...}

void enableRawMode() {...}

char editorReadKey() {...}

/** output ***/
void editorRefreshScreen() {
    write(STDOUT_FILENO, "\x1b[2J", 4);
    write(STDOUT_FILENO, "\x1b[H", 3);
}

/*** input ***/

void editorProcessKeypress() {
    char c = editorReadKey();

    switch(c) {
        case CTRL_KEY('q'):
            write(STDOUT_FILENO, "\x1b[2J", 4);
            write(STDOUT_FILENO, "\x1b[H", 3);
            exit(0);
            break;
    }
}

/*** init ***/

```

---
Next we start rendering things on our screen:
[Drawing Tildes on Screen](../03-raw-input-and-output/05-tildes.md)
