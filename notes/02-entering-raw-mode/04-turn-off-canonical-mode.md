There's this `ICANON` flag which we can disable to fully turn off canonical mode. By disabling it we will finally read input byte-by-byte rather than line-by-line.

`ICANON` comes from `<termios.h>` library. AND `ICANON` flag, even though it has an 'i' in it, it is NOT a input flag like `c_iflag` rather it is a local flag in the `c_lflag` field.

And when this is implemented, the program quits when you press `q`.

---
#### Code Snippet:

```C
#include <stdlib.h>
#include <termios.h>
#include <unistd.h>

struct termios org_termios;
void disableRawMode() {...}

void enableRawMode() {
	tcgetattr(STDIN_FILENO, &org_termios);
	atexit(disableRawMode);
	
	struct termios raw = org_termios;
	raw.c_lflag &= ~(ECHO | ICANON); //The Only Change.
	
	tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}

int main() {...}
```

---

Next we will display key presses which are taken as a input but are not displayed yet:
[Display Keypresses](../02-entering-raw-mode/05-display-keypresses.md)
