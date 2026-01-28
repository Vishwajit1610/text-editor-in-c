Now to get the window dimensions, we can use the `ioctl()` with the `TIOCGWINSZ` request.

`ioctl()`, `TIOCGWINSZ` and `struct winsize` comes from `<sys/ioctl.h>`. 

`ioctl()` asks the terminal driver that, "How big is you window?" and `TIOCGWINSZ` means Terminal I/O Control - Get WINdow SiZe.

`ioctl()` returns -1 when failure occurs and when succeeded, we pass the values back by setting the `int` references that were passed to the function. (This is a common approach to having functions return multiple values in C. It also allows you to use the return value to indicate success or failure.)

---
#### Code Snippet 

```C
/*** includes ***/

#include <ctype.h>
#include <errno.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/ioctl.h>   
#include <termios.h>
#include <unistd.h>

/*** defines ***/

#define CTRL_KEY(k) ((k) & 0x1f)

/*** data ***/
/*** terminal ***/

void die(const char *s) {...}

void disableRawMode() {...}

void enableRawMode() {...}

char editorReadKey() {...}

int getWindowSize(int *rows, int *cols) {
    struct winsize ws;

    if (ioctl(STDOUT_FILENO, TIOCGWINSZ, &ws) == -1 || ws.ws_col == 0) {
        return -1;
    } else {
        *cols = ws.ws_col;
        *rows = ws.ws_row;
        return 0;
    }
}

/** output ***/

void editorDrawRows() {
    int y;
    for (y = 0; y < E.screenrows; y++) {
        write(STDOUT_FILENO, "~", 1);

        if (y < E.screenrows - 1) {
            write(STDOUT_FILENO, "\r\n", 2);
        }
    }
}

void editorRefreshScreen() {...}

/*** input ***/

void editorProcessKeypress() {...}

/*** init ***/
void initEditor() {
    if (getWindowSize(&E.screenrows, &E.screencols) == -1) die("getWindowSize");
}

int main() {
    enableRawMode();
    initEditor();

	while(1) {
        editorRefreshScreen();
        editorProcessKeypress();
    }
	
	return 0;
}
```

