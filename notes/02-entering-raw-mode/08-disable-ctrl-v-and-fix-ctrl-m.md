## Disable Ctrl-V behavior:

On some systems, when you press `Ctrl + V`, on some systems you might face a waiting time in which terminal waits for you to type another character and then sends that character directly. 

This issue I have not faced, still we will fix it for others. 

This behavior can be fixed by simply turning off the `IEXTEN` flag, which comes from `<termios.h>` library. And is another one of those flags which starts with an 'i' but belongs in the `c_lflag` (local flag) field.

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
	raw.c_iflag &= ~(IXON);
	raw.c_lflag &= ~(ECHO | ICANON | ISIG | IEXTEN); // Disable IEXTEN Flag.

	tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}

int main() {...}
```

---

## Fix Ctrl-M behavior:

Right now, when you go through all the alphabets holding `Ctrl`, from `A-Z`, you would get output from 1-26. EXCEPT `Ctrl + M` will give you 10 as output. It get's read as 10 even though it should be 13 because it's the 13th letter of alphabet.

This happens because `Ctrl + M` generates a carriage return (13, `'\r'`) and the terminal enabled with `ICRNL` translates carriage returns into newlines (10, `'\n'`).

Only the `Ctrl + J` and the `Enter` key produces output as 10. 

So in short the terminal translates any carriage returns (13, `'\r'`) into newline (10, `'\n'`).

`ICRNL` comes from `<termios.h>` and `I` stands for input flag, `CR` for carriage returns and `NL` for newline.

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
	raw.c_iflag &= ~(IXON | ICRNL); // Disable ICRNL Flag.
	raw.c_lflag &= ~(ECHO | ICANON | ISIG | IEXTEN);

	tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}

int main() {...}
```

Now `Ctrl + M` will be read as 13 (Carriage return) and `Enter` key will also be read as 13.

---
Next we will turn off all of the output processing since the terminal does this same behavior on the output side, and translates all newlines (`'\n'`) we print into a carriage return followed by a newline (`'\r\n'`).
[Turn off all output processing](../02-entering-raw-mode/09-turn-off-all-output-processing.md)

---
