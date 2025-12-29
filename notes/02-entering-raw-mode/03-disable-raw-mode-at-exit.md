To give the user reassurance, we give their terminal after the program is terminated.

To do this we first declare the structure or the original terminal state in global scope on purpose.  

```C
struct termios org_termios;
```

THEN we create a function whose purpose is ONLY to restore the terminal. No Logic. No Flags. No Conditionals.

```C
void disableRawMode() {
	tcsetattr(STDIN_FILENO, TCSAFLUSH, &orig_termios);
}
```

We also use the `atexit()` function which comes from `<stdlib.h>`, and is used to register our `disableRawMode()` function to be called automatically when the program exits.

We assign the `orig_termios` struct to the `raw` struct in order to make a copy of it before we start making our changes.

---

#### Code Snippet:

```C
#include <stdlib.h>
#include <termios.h>
#include <unistd.h>

struct termios orig_termios;

void disableRawMode() {
	tcsetattr(STDIN_FILENO, TCSAFLUSH, &orig_termios);
}

void enableRawMode() {

	tcgetattr(STDIN_FILENO, &orig_termios);
	atexit(disableRawMode);

	struct termios raw = orig_termios;
	raw.c_lflag &= ~(ECHO);

	tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}

int main() {
	enableRawMode();

	char c;
	while (read(STDIN_FILENO, &c, 1) == 1 && c != 'q');
	return 0;
}
```

---

The article says, *"You may notice that leftover input is no longer fed into your shell after the program quits. This is because of the `TCSAFLUSH` option being passed to `tcsetattr()` when the program exits"*.

No difference for me as I was not facing any issue in the first place. 

---
Now to turn off the last flag which will fully turn off canonical mode: 
[Turn off Canonical Mode](../02-entering-raw-mode/04-turn-off-canonical-mode.md)

