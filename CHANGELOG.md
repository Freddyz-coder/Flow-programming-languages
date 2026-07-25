# Flow Changelog

## v5.0 — Enhanced Edition

### New Features

- **REPL**: `flow` (no args) → interactive prompt with multi-line block support
- **String interpolation**: `{expr}` holes inside any string literal
- **`use` module system**: vault, cargo, reckon, pulse, prose, weave, echo
- **`prove(cond[, msg])`**: assertions — stop execution if condition is false
- **`argv`**: built-in box of command-line arguments; `argv index 0` is the script path
- **`exit(code)`**: terminate the script with a given exit code
- **`gather(box, func)`**: map — apply a function to every element
- **`sift(box, func)`**: filter — keep elements where func returns truthy
- **`fold(box, start, func)`**: reduce — accumulate into a single value
- **`not` keyword**: alternative to `!` for logical negation
- **`weave` module**: `gather`, `sift`, `fold`, `each_with`, `tally`
- **`echo` module**: `stamp`, `pad`, `center`, `repeat` for text formatting
- **`cargo` module**: `tally`, `align`, `flip`, `peek`, `pluck`, `shove`, `wedge`
- **`reckon` module**: `peak`, `pit`, `flatten`, `raise`, `boost`, `root`, `drift`
- **`pulse` module**: `beat`, `tick`, `stall`
- **`prose` module**: `shave`, `shout`, `whisper`, `holds`, `cleave`, `stitch`, `swap`, `glyph`
- **`vault` module**: `fetch`, `inscribe`, `stack`, `scratch`, `exists`
- **`string * int`**: multiplies a string — `"ha" * 3` → `"hahaha"`

### Bug Fixes (inherited from v1)

- Real block scoping (check/loop/again/aslong/group)
- String indexing with `index`
- Chained dot access and `index`+dot mixing
- `and`/`or` short-circuit operators
- Boxes/maps are shared reference types (mutations in functions affect the original)
- `==`/`!=` on boxes and maps compares contents, not identity
- `arr index i = value;` and `m.field = value;` assignment targets
- `%` works with floats
- `true`/`false` castable with `number >>`

## v1.0 — Initial Release

- Lexer, parser, and tree-walking interpreter
- Variables, constants (`always`), scoping
- `check`/`then`/`else`, `loop`, `again`, `aslong`, `each`
- `put`/`take` functions with closures
- `box`, `map`, `index`, dot-access
- `attempt`/`catch` error handling
- `import` for multi-file scripts
- Basic stdlib: math, string, I/O, type introspection
