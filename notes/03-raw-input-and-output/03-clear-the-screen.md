Since we are working on a text-editor, we would require to have our screen cleared. THAT is what we will do in this step before implementing key-presses.

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
/** output ***/
void editorRefreshScreen() {
    write(STDOUT_FILENO, "\x1b[2J", 4);
}

/*** input ***/
/*** init ***/

int main() {
	enableRawMode();
    editorRefreshScreen();
    
	while(1) {
        editorProcessKeypress();
    }
	
	return 0;
}
```

---
The `write()` and `STDOUT_FILENO` comes from `<unistd.h>`.
The `4` in the `write()` means we are writing `4 bytes`. 
The first byte is `\x1b` which is a *escape character* or `27` in decimal.
The other three bytes are `[2J` where `2J` cleans the entire screen.

Specifically ->
- `0J` → clear from cursor to end
    
- `1J` → clear from start to cursor
    
- `2J` → clear entire screen

Now if we were to execute the program, then the commands before the execute command will disappear and the entire screen will be empty.

---
Now you may notice that, when the program is executed the cursor stays exactly on the line on which the command for executing the program was done, therefore in the next step we do:
[Repositioning the Cursor](../03-raw-input-and-output/04-reposition-the-cursor.md)
