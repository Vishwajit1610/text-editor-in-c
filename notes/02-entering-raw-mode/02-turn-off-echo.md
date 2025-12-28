
"Raw Mode" is achieved by **disabling multiple terminal flags.**
We start with the most  visible flag among all - `Echo`.

---
First thing we do is turn off the `Echo` feature, what it does is -> **when a key is pressed, it prints it to the terminal.** This is ONE OF THE things we have to turn off to achieve RAW mode.

This feature is good in Canonical Mode, but we would want to get rid of it while switching to RAW mode. 

When **you DO turn this off,** it doesn't print what you type and **it acts similar to what appears when you are trying to enter password in sudo mode inside Linux.**

---
To implement this in OUR text-editor program, we make use of the `<termios.h>` library which consists of:

1. `struct termios` 
2. `tcgetattr()` -> read current settings.
3. `tcsetattr()` -> apply modified settings.
4. `TCSAFLUSH` -> apply changes and discard unread input and is an argument for `tcsetattr()` function.
5. `ECHO` -> causes each key you type to be printed to the terminal.

Some other fields:

6. `c_lflag` -> for Local flags.
7. `c_iflag` -> input flags.
8. `c_oflag` -> output flags.
9. `c_cflag` -> control flags.

This is all which we have to modify to to enable raw mode.

---
#### Code Snippet:

```C
#include <termios.h>
#include <unistd.h>

void enableRawMode() {
	struct termios raw;

	tcgetattr(STDIN_FILENO, &raw);

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
When the code has been implemented, your shell might not fully exit from the RAW mode when program is terminated and you might face no `Echo` when you enter something.

This can be solved by starting a fresh new line of input to your shell by doing `Ctrl-C`, and type `reset` and `Enter`. This resets your terminal back to normal. 

*"We will fix the whole problem in the next step",* is what the article says.

For me personally, I didn't face any of this issue and I am using `zsh 5.9`.

---

Next we will FIX the terminal not Echoing inputs for the user: [Disable RAW mode at exit](../02-entering-raw-mode/03-disable-raw-mode-at-exit.md)


