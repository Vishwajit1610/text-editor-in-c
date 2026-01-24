From now on, we will start drawing some things on our text-editor. 

First we will draw tildes `~` on left side similar to VIM editor. 

For this we will make a new function which will handle the drawing of tildes on each row. And those rows are not part of the file and can't contain any text just like VIM.

After drawing we do another `ESC[H` to reposition our cursor back to top-left corner.

---
#### Code Snippet:

```C
/*** includes ***/
/*** defines ***/
/*** data ***/
/*** terminal ***/
/** output ***/

void editorDrawRows() {
    int y;
    for (y = 0; y < 30; y++) {
        write(STDOUT_FILENO, "~\r\n", 3);
    }
}

void editorRefreshScreen() {
    write(STDOUT_FILENO, "\x1b[2J", 4);
    write(STDOUT_FILENO, "\x1b[H", 3);

    editorDrawRows();

    write(STDOUT_FILENO, "\x1b[H", 3); 
}

/*** input ***/
/*** init ***/
```

---
For now we print 30 tildes `~` and later on we would think about adding more.

But before adding more, we would need to know the size of the terminal to draw tildes inside the editor.

To do that, we first initialize a global structure which will contain our editor state which we will use to store the width and height of the terminal.

---
#### Code Snippet:

```C
/*** includes ***/
/*** defines ***/
/*** data ***/

struct editorConfig {
    struct termios orig_termios;
};

struct editorConfig E;

/*** terminal ***/

void die(const char *s) {...}

void disableRawMode() {
    if(tcsetattr(STDIN_FILENO, TCSAFLUSH, &E.orig_termios) == -1)
        die("tcsetattr");
}

void enableRawMode() {

    if(tcgetattr(STDIN_FILENO, &E.orig_termios) == -1) die("tcgetattr");
    atexit(disableRawMode);


	struct termios raw = E.orig_termios;
	raw.c_iflag &= ~(IXON | ICRNL | BRKINT | INPCK |ISTRIP);
	raw.c_oflag &= ~(OPOST);
	raw.c_cflag |= (CS8);
	raw.c_lflag &= ~(ECHO | ICANON | ISIG | IEXTEN);
    raw.c_cc[VMIN] = 0;
    raw.c_cc[VTIME] = 10;

	if(tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw) == -1)
        die("tcsetattr");
}

char editorReadKey() {...}

/** output ***/
/*** input ***/
/*** init ***/
```

We replace all the `orig_termios` occurrences with `E.orig_termios`.

---
Next we will get the window size and restructure our tildes:
[Window Size and restructure tildes](../03-raw-input-and-output/06-window-size.md))