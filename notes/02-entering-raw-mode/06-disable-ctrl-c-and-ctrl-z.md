In Unix-like operating systems, **`SIGINT` terminates a process by default** and is typically initiated by pressing **`Ctrl+C`**, while **`SIGTSTP` temporarily suspends a process** (stops its execution) and is initiated by pressing **`Ctrl+Z`**.

This behavior is NOT WHAT WE WANT. Therefore, we simply disable this behavior.

---
We simply turn off the `ISIG` flag which is NOT a Input flag even though it start with an 'i' rather it is a flag which gives actions such as (QUIT)`Ctrl + C` and (Suspend)`Ctrl + Z`.

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
	raw.c_lflag &= ~(ECHO | ICANON | ISIG); //Only update to make.

	tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}

int main() {...}
```

---

Now `Ctrl + Z` is read as 26 byte and `Ctrl + C` is read as 3 byte. 

---
Next we disable the `Ctrl + S` and `Ctrl + Q` behavior:
[Disable Ctrl + S and Ctrl + Q](../02-entering-raw-mode/07-disable-ctrl-s-and-ctrl-q)
