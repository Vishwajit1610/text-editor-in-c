The SETUP is a crucial prerequisite step before building a project. 

To check if the dependencies are present in our system, To check if the compilers are installed and stuff.

Funnily enough, the article explicitly states that, *"The program we are building doesn't depend on any external libraries"*. So that is that.

So the main thing we have to **check is if the C compiler is present/installed in our system.**

This can be done using this command in your CMD (for WINDOWS) or terminal (for LINUX) or too broke to be using MAC yet. 

```shell
cc --version
```

**The cc literally stands for 'C Compiler'.**

ALSO, we will be using `make` program, and to check if it's present we can run.

```shell
make -v
```

---

The reason for using `make` is, **C is a compiled language.** And for us to RUN a program, we first compile the `.c` into a executable file using this command inside your shell.

```shell
cc file_name.c -o file_name
```
This command compiles the file and gives us an executable file. 

And THEN we run this command to finally run our program.

```shell
./file_name
```

And then our programs provides the specific tasked output.

---

Doing all THIS is a teadeous task and **WE CAN'T be recompiling every time we make small changes, this is where `make` comes in.** 

`make` let's you simply run `make` and compile your program for you. You just have to provide a MakeFile and tell in that how to compile your program.

This can be done by creating a `make` file named 'Makefile'. And here we are taking the example from the article in which they've set their program name as `kilo.c`.

In that 'Makefile', you insert this code, telling it HOW you want it to compile your program.

```c
kilo: kilo.c	
	$(CC) kilo.c -o kilo -Wall -Wextra -pedantic -std=c99
```

- The first line tells it that, we want to build `kilo`. And that `kilo.c` is what is required to build it. 

- The second line specifies the command to run in order to actually build `kilo` out of `kilo.c`.
- `$(CC)` is a variable that `make` expands to `cc` by default.
- `-Wall` stands for “**all** **W**arnings”, and gets the compiler to warn you when it sees code in your program that might not technically be wrong, but is considered bad or questionable usage of the C language, like using variables before initializing them.
- `-Wextra` and `-pedantic` turn on even more warnings. For each step in this tutorial, if your program compiles, it shouldn’t produce any warnings except for “unused variable” warnings in some cases. If you get any other warnings, check to make sure your code exactly matches the code in that step.
- `-std=c99` specifies the exact version of the C language **st**an**d**ard we’re using, which is [C99](https://en.wikipedia.org/wiki/C99) C99 allows us to declare variables anywhere within a function, whereas **[ANSI C](https://en.wikipedia.org/wiki/ANSI_C) requires all variables to be declared at the top of a function or block.** 

---

In this SETUP, I've also followed the article and created kilo.c file AND I have learned HOW to compile, run a C program. 

AND compile using 'Makefile' or `make` command and run the C program.

I have been taught that, if I were to make changes inside the kilo.c file such as return 100 in place of return 0 and compile and run the file. I would of course not get an output. But by doing the command `echo $?` we get the return code which we set.

AND return code 0 means success.

NOW, by creating the 'Makefile' then inserting the contents of HOW we want our file to be compiled then running the `make` command, FROM THAT POINT ONWARDS we can just simply run `make` to compile and `./kilo` to run our program. 

**The point to note here is:** 

- If `kilo` was last modified after `kilo.c` was last modified, then `make` assumes that `kilo.c` has already been compiled, and so it doesn’t bother running the compilation command. 
- If `kilo.c` was last modified after `kilo` was, then `make` recompiles `kilo.c`.

---
#### Code Snippet: 

```C
#include <unistd.h>

int main() {
	char c;
	while (read(STDIN_FILENO, &c, 1) == 1 && c != 'q');
	return 0;
}
```

---
Next:
[Canonical vs Non-Canonical Mode](../02-entering-raw-mode/01-canonical-vs-non-canonical-mode.md)

