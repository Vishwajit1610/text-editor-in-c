## Append Buffer
- It's not a good idea to be using `write()` for every action. 
- It's better to write a single time rather than multiple. 

Every screen refresh was doing 20+ small `write()` calls (one per tilde row).
To fix this, we build the entire frame in memory first, as one string, `write()` it once.

C has no built-in dynamic string type (unlike Python str, JS string — those resize themselves). Have to hand-build one: a growable buffer + a length.

The Struct

```
struct abuf {
  char *b;   // pointer to a block of bytes — the buffer itself
  int len;   // how many bytes currently used
};
#define ABUF_INIT {NULL, 0}
```

`char *b` is a pointer, not an array. Array = fixed size, fixed at compile time. Pointer = "address of some memory, size decided at runtime, can be reassigned to point elsewhere." 
Needed here because total screen content size isn't known ahead of time and it grows as you append.

`ABUF_INIT` = starting value: pointer to nothing (`NULL`), length 0. Shorthand for `struct abuf ab = {NULL, 0};`.

The core concept to understand here is -> Pass-by-Value vs Pass-by-Pointer
C is pass-by-value. Pass a struct directly into a function → function gets a copy. Mutating the copy does nothing to the caller's original.

To let a function actually modify the caller's data, we need to pass the address of it instead.

- `&` = "give me the address of X" (turns a value into a pointer to it)
- `*` in a declaration = "this variable holds an address" (defines a pointer)
- `->` = "I have a pointer, reach through it to a field"
- `.` = "I have the actual struct in hand, access a field directly"

`abAppend`

```
void abAppend(struct abuf *ab, const char *s, int len) {
  char *new = realloc(ab->b, ab->len + len);
  if (new == NULL) return;
  memcpy(&new[ab->len], s, len);
  ab->b = new;
  ab->len += len;
}
```

`realloc(old_pointer, new_size_in_bytes)`. Asks the OS to resize a memory block. 

Three possible outcomes:
1. Room to grow in place → does that, returns the same address.
2. No room → allocates a new block elsewhere big enough, copies old content over automatically, frees the old block, returns the new address.
3. Can't get memory at all → returns NULL. Old block is left untouched.

Why assign to a temp new instead of ab->b directly? 
Because case 3 is the trap. If you wrote: `ab->b = realloc(ab->b, ...);` and it fails, ab->b becomes NULL and now you've just overwritten your only pointer to the original data with nothing. 
That memory block still exists somewhere, but you've lost the address to it. Unrecoverable leak.
Correct pattern: capture result in a new, separate variable, check for NULL, only commit to ab->b once you know it's valid.

`abFree`
C has no garbage collector. Every `malloc/realloc` is a debt and must be paired with exactly one `free()`, or that memory is unreachable until the process exits ("leaked"). This function pays the debt.

`memcpy(destination, source, byte_count)` — raw byte copy, no type awareness, no interpretation.

- `&new[ab->len]` = address of index `ab->len` within new. 
- Index 0 = start of buffer. 
- Index `ab->len` = the position right after everything already stored. 
- `&` turns "the value at that index" into "the address of that index" — because memcpy needs a destination address, not a value.

So: copy `len` bytes from `s`, landing right after existing content. Old bytes at the front stay untouched and this is the actual "append."

`ab->b = new; ab->len += len;`
Commit the (now validated) new pointer, update length to match. Struct now correctly reflects bigger buffer + more content.

Where It's Used:

```
void editorRefreshScreen() {
  struct abuf ab = ABUF_INIT;          // real struct, lives on the stack here

  abAppend(&ab, "\x1b[2J", 4);         // &ab — pass address so function can mutate original
  abAppend(&ab, "\x1b[H", 3);

  editorDrawRows(&ab);                 // draws tildes INTO the buffer, not the screen

  abAppend(&ab, "\x1b[H", 3);

  write(STDOUT_FILENO, ab.b, ab.len);  // the ONLY write to the actual screen — whole frame at once
  abFree(&ab);                         // pay back the memory debt before returning
}
```

Note `ab.b` here uses `.` because this function holds the actual struct, not a pointer to it. 
Everything before the final `write()` happens invisibly, in memory. 
That one `write()` call is the entire payoff: 1 syscall instead of ~20+, no gaps between them, no flicker.

## Hide the cursor when repainting

It’s possible that the cursor might be displayed in the middle of the screen somewhere for a split second while the terminal is drawing to the screen.
To make sure that doesn’t happen, let’s hide the cursor before refreshing the screen, and show it again immediately after the refresh finishes.

```
void editorRefreshScreen() {
  struct abuf ab = ABUF_INIT;

  abAppend(&ab, "\x1b[?25l", 6);        # <-
  abAppend(&ab, "\x1b[2J", 4);
  abAppend(&ab, "\x1b[H", 3);

  editorDrawRows(&ab);

  abAppend(&ab, "\x1b[H", 3);
  abAppend(&ab, "\x1b[?25h", 6);        # <-

  write(STDOUT_FILENO, ab.b, ab.len);
  abFree(&ab);
}
```

We use escape sequences to tell the terminal to hide and show the cursor. The h and l commands (Set Mode, Reset Mode) are used to turn on and turn off various terminal features or “modes”.

