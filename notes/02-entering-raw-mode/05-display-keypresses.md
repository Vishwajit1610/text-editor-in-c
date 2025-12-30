In this step, we learn about characters and their [ASCII Values](https://www.ascii-code.com/). 
Foremost, we will be printing the key's pressed and their corresponding ASCII values which has been given to them. 

For example, when you press 'A' you would get '65' as an output, and when you press 'a' you will get '97' as an output. The values are just the characters ASCII values. 

**There are a total 256 ASCII Values, starting from '0' which is 'NULL' till '255' which is 'ÿ'.** 

---
To perform this step, we will make use of two prominent libraries in C:

1. `<ctype.h>` 
	- Provides a collection of standard functions for **classifying and transforming individual characters**
	- Provides functions such as: `isalnum()`, `isalpha()`, `isdigit()`, `iscntrl()`, etc.

2. `<stdio.h>` 
	- Provided a collection of functions for **performing input and output operations.**
	- Provides functions such as: `printf()`, `scanf()`, `getchar()`, `putchar()`, etc.

---
We will be using the `iscntrl()` and `printf()` functions from `<ctype.h>` and `<stdio.h>` libraries respectively.

1. `iscntrl()` - tests whether a character is a control character. Control characters are the one's which we DO NOT want to be printed on our screen. ASCII codes from 0 - 31 are all control characters, also ASCII code 127 is a control character.
	From ASCII code 32 - 126, all are printable characters. 

2. `printf()` - will print the output as we want, `%d` for integer value / ASCII code and `%c` for the actual character. 

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

void enableRawMode() {...}

int main() {
	enableRawMode();

	char c;
	while (read(STDIN_FILENO, &c, 1) == 1 && c != 'q') {
		if (iscntrl(c)) {
			printf("%d\n", c);
		} else {
			printf("%d ('%c')\n", c, c);
		}
	}
	
	return 0;
}
```

---
After you execute this program, when you press a key then you will see the character and it's corresponding ASCII code.

For printable characters, the ASCII value will be printed alongside it.

For control characters, ONLY the ASCII value will be printed and NOT the actual character that is being pressed. 

You will notice for characters such as `PgUP`, `PgDn`, `Delete`, `Enter`, they print 3 or 4 bytes. This is because they are called `Escape Sequence characters` and they start with byte 27. Thus you see the output for `PgDn` as -> 

```
27
91 ('[')
54 ('6')
126 ('~')
```

---
Pressing `Ctrl + s` will tell the program to suspend the output and so it will be in a frozen state. To fix this you simply press `Ctrl + q` which will resume the session.

For me, it directly gives a prompt when `Ctrl + s` is pressed and tells me that, *"The output has been suspended to resume press `Ctrl + q`".*

Also, when `Ctrl + z` is pressed the program will be suspended to the background, it will LOOK LIKE it's terminated but it will be still running, to get back use the `fg` command to get back in and then continue where it was suspended.

And finally, `Ctrl + c` or q will STILL terminate the program. 

In the next few steps, we will turn off the `Ctrl + z`, `Ctrl + q`, `Ctrl + c`, `Ctrl + s` default actions to make our RAW mode work in a full fledged manner.

---

Next Step: [Disable Ctrl + S and Ctrl + Q](../02-entering-raw-mode/06-disable-ctrl-c-and-ctrl-z.md)

