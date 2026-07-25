# Flow Language — Feature Reference (v2)

Flow is a dynamically typed, interpreted scripting language with a unique,
minimal syntax. It is designed to be friendly for beginners without copying
the look of Python, JavaScript, or any other existing language.

---

## What's new in v2

### 1. REPL — Interactive Mode

Running `flow` with no arguments opens an interactive prompt:

```
flow> >> name = "World";
flow> "Hello, {name}!" << show;
Hello, World!
flow> quit
```

- Multi-line blocks (those containing `{`) are supported — keep typing until
  the braces balance, then press Enter.
- Type `quit` or press Ctrl-D to exit.

---

### 2. String Interpolation

Any string can contain `{expression}` holes. The expression inside is
evaluated at runtime and its result is embedded in the string:

```flow
>> name = "Flow";
>> n = 42;
"Hello, {name}! The answer is {n * 2}." << show;
~~ → Hello, Flow! The answer is 84.
```

Function calls, arithmetic, comparisons — all valid inside `{...}`:

```flow
put cube(x) { take x * x * x; }
"Cube of 3 is {cube(3)}" << show;
```

Use `\{` to include a literal `{` without triggering interpolation:

```flow
>> tmpl = "Dear \{name},";     ~~ stores the literal text {name}
```

---

### 3. Module System — `use`

Load a built-in module with `use modulename;`. The module's functions become
available under `modulename.function(...)`:

```flow
use prose;
prose.shout("hello") << show;    ~~ → HELLO
```

---

#### `use vault;` — File storage

| Function | Replaces | Description |
|---|---|---|
| `vault.fetch("file")` | `read` | Retrieve file contents as a letter |
| `vault.inscribe("file", data)` | `write` | Overwrite a file with data |
| `vault.stack("file", data)` | `append` | Stack data at the bottom of a file |
| `vault.scratch("file")` | `delete_file` | Remove a file |
| `vault.exists("file")` | `exists` | Check if a file exists → bool |

```flow
use vault;
vault.inscribe("log.txt", "First entry\n");
vault.stack("log.txt", "Second entry\n");
>> lines = vault.fetch("log.txt");
lines << show;
```

---

#### `use cargo;` — Box (array) tools

| Function | Replaces | Description |
|---|---|---|
| `cargo.tally(box)` | `length` | Count items in a box |
| `cargo.align(box)` | `sort` | Return a sorted copy (ascending) |
| `cargo.flip(box)` | `reverse` | Return a reversed copy |
| `cargo.peek(box)` | last item | Read the last item without removing |
| `cargo.pluck(box)` | `pop` | Remove and return the last item |
| `cargo.shove(box, item)` | `push` | Push item onto the end |
| `cargo.wedge(box, i, item)` | `insert_at` | Insert at position `i` |

```flow
use cargo;
>> scores = box [9, 2, 7, 1];
>> count = cargo.tally(scores);
"Items in box: {count}" << show;
"Sorted: {cargo.align(scores)}" << show;
```

---

#### `use reckon;` — Math estimation

| Function | Replaces | Description |
|---|---|---|
| `reckon.peak(a, b)` | `max` | Highest of two values |
| `reckon.pit(a, b)` | `min` | Lowest of two values |
| `reckon.flatten(n)` | `floor` | Crush a decimal down |
| `reckon.raise(n)` | `ceil` | Lift a decimal up |
| `reckon.boost(base, exp)` | `pow` | Power: base^exp |
| `reckon.root(n)` | `sqrt` | Square root |
| `reckon.drift(n)` | `abs` | Absolute value |

```flow
use reckon;
>> highest = reckon.peak(45, 12);
"The top value is: {highest}" << show;
```

---

#### `use pulse;` — Time and rhythm

| Function | Replaces | Description |
|---|---|---|
| `pulse.beat()` | `Date.now()` | Current Unix timestamp in milliseconds |
| `pulse.tick()` | `time()` | Current Unix timestamp in seconds |
| `pulse.stall(ms)` | `sleep` | Freeze the script for N milliseconds |

```flow
use pulse;
>> start = pulse.beat();
pulse.stall(500);
"Elapsed: {pulse.beat() - start}ms" << show;
```

---

#### `use prose;` — Text as spoken words

| Function | Replaces | Description |
|---|---|---|
| `prose.shave(s)` | `trim` | Shave whitespace from both edges |
| `prose.shout(s)` | `upper` | Convert to uppercase |
| `prose.whisper(s)` | `lower` | Convert to lowercase |
| `prose.holds(hay, needle)` | `contains` | Check if text holds a fragment |
| `prose.cleave(s, sep)` | `split` | Split text by a separator |
| `prose.stitch(box, sep)` | `join` | Join array into text |
| `prose.swap(s, old, new)` | `replace` | Replace all occurrences |
| `prose.glyph(s, i)` | char at index | Get character at position i |

```flow
use prose;
>> raw = "   flow language   ";
>> clean = prose.shout(prose.shave(raw));
clean << show;    ~~ → FLOW LANGUAGE
```

---

#### `use weave;` — Higher-order collection helpers

| Function | Description |
|---|---|
| `weave.gather(box, func)` | Map: apply func to every element → new box |
| `weave.sift(box, func)` | Filter: keep elements where func returns truthy |
| `weave.fold(box, start, func)` | Reduce: accumulate into a single value |
| `weave.each_with(box, func)` | Iterate with `func(index, value)` |
| `weave.tally(box)` | Count items (same as cargo.tally) |

```flow
use weave;
>> nums = box [1, 2, 3, 4, 5];

put double(x) { take x * 2; }
"Doubled: {weave.gather(nums, double)}" << show;

put even(x) { take x % 2 == 0; }
"Evens: {weave.sift(nums, even)}" << show;

put add(acc, x) { take acc + x; }
"Sum: {weave.fold(nums, 0, add)}" << show;
```

The top-level functions `gather(box, func)`, `sift(box, func)`, and
`fold(box, start, func)` are also available globally without `use weave;`.

---

#### `use echo;` — Text formatting

| Function | Description |
|---|---|
| `echo.stamp(template, map)` | Fill `\{key}` holes in a template from a map |
| `echo.pad(s, width)` | Right-pad string to width with spaces |
| `echo.pad(s, width, char)` | Right-pad with a specific character |
| `echo.center(s, width)` | Center string within width |
| `echo.repeat(s, n)` | Repeat string n times |

```flow
use echo;
>> tmpl = "Dear \{name}, your score is \{score}.";
echo.stamp(tmpl, map { name: "Alice", score: 98 }) << show;
```

Note: Use `\{` inside a template string to prevent interpolation — the
template's placeholders are filled by `echo.stamp` at call time.

---

### 4. `prove` — Assertions

`prove(condition)` or `prove(condition, "message")` — stops the program with
an error if the condition is falsy:

```flow
prove(1 + 1 == 2);
prove(length("hello") == 5, "Unexpected string length");
"All checks passed." << show;
```

Use `prove` to validate your assumptions during development or as simple
unit tests embedded in your script.

---

### 5. `argv` — Command-line arguments

Every script gets an `argv` box automatically:

- `argv index 0` → the script name
- `argv index 1` → the first argument
- `argv index 2` → the second argument, etc.

```flow
>> count = length(argv) - 1;
"Script: {argv index 0}" << show;
"Arguments: {count}" << show;
```

Run with: `flow myscript.flow arg1 arg2`

---

### 6. `exit(code)` — Exit with a status code

```flow
check some_condition then {
    "Something went wrong." << show;
    exit(1);
}
exit(0);
```

---

### 7. `not` keyword

As an alternative to `!`, you can write `not` for logical negation:

```flow
check not (x == 0) then {
    "x is non-zero" << show;
}
```

---

### 8. Higher-order builtins (no module needed)

`gather`, `sift`, and `fold` are available globally without `use weave;`:

```flow
put square(x) { take x * x; }
>> squares = gather(box [1, 2, 3, 4, 5], square);
squares << show;
```

---

## Existing features (unchanged from v1)

### Output & Formatting
- `value << show;` — print with newline
- `value << show:` — print without newline
- `Add.space`, `Add.tab`, `Add.newline` for chained output
- Count modifiers: `Add.space.3`, `Add.tab.2`

### Variables & Scoping
- `>> name = value;` — declare variable
- `always PI = 3.14159;` — declare constant (no reassignment)
- Real block scoping: variables in `{ }` stay there; assignments to outer
  variables update the outer binding

### Data Types
- **Numbers**: `123`, `3.14`
- **Words (strings)**: `"hello"`, `"Hello, {name}!"` (with interpolation)
- **Booleans**: `true`, `false`
- **Boxes (arrays)**: `box [1, 2, 3]` — accessed with `index`
- **Maps**: `map { key: "value" }` — accessed with `.field`
- **Unit**: empty value returned by some statements

### Control Flow
- `check cond then { ... } else { ... }` — conditional
- `loop { ... }` — infinite loop (use `break` to exit)
- `again 3 { ... }` — repeat N times
- `aslong cond { ... }` — while loop
- `each item in collection { ... }` — iterate over box/map/string
- `each key, value in map { ... }` — iterate with key and value
- `break` — exit a loop early

### Functions
- `put name(params) { body; take result; }` — define a function
- `take value;` — return a value

### Error Handling
- `attempt { ... } catch err { ... }` — catch runtime errors
- `attempt { ... } catch { ... }` — catch without binding the error

### Imports
- `import "file.flow";` — include another Flow file

### Type Casts
- `number >> expression` — cast to number
- `letter >> expression` — cast to string

### Comments
- `~~ single line comment`
- `~~~ multi-line comment ~~~`

### Global Built-ins
`ask`, `length`, `upper`, `lower`, `trim`, `replace`, `split`, `join`,
`slice`, `contains`, `push`, `pop`, `remove`, `insert_at`,
`sqrt`, `abs`, `min`, `max`, `round`, `floor`, `ceil`, `random`,
`type`, `to_number`, `to_letter`, `keys`, `values`,
`read`, `write`, `append`, `exists`, `delete_file`,
`gather`, `sift`, `fold`, `prove`, `exit`
