# paradex

a small vertical retro-mac terminal widget for cataloguing the aesthetics,
moods, and motifs you keep coming back to — then pulling a random set from
that catalogue to seed a new project.

stdlib-only Python (`curses` + `tkinter`), single file, no dependencies.

## run

```
paradex            # in-terminal, same as `paradex tui`
paradex window      # standalone desktop widget — no host terminal, draws its own mac-style chrome
```

`window` mode opens its own top-level window (WM_CLASS `paradex`) and
daemonizes by default; pass `--fg` to keep it attached to the launching
shell, `--geometry WxH+X+Y` to place it, `--font-size N` to resize.

(symlinked at `~/.local/bin/paradex` → `paradex` in this repo)

## commands

```
add <category> <text> [#tag ...]   file a new entry
list [category|#tag]  (ls)         show entries, optionally filtered
tag <id> #tag [#tag ...]           add tags to an entry
rm <id>  (del)                     remove an entry
roll [n] [category|#tag]           randomly pull n entries (default 3)
build [name]                       save the last roll as a build sheet
categories  (cat)                  list categories + counts
find <term>                        search text/tags/category
clear                               drop the active filter
help                                command reference
quit  (:q, exit)                   leave
```

## example

```
add palette black voidscape, red neon veins #hackercore #ritual
add mood restless midnight circuitry #glitch
add mechanic feed a wraith fragments of stolen uptime #zahl #idle
roll 3
build my-next-thing
```

`build` writes a plain-text seed sheet to `builds/<name>.txt` grouping the
pulled entries by category, with a blank line at the bottom to fill in
what you're building.

## storage

entries persist to `~/.local/share/paradex/db.json`. build sheets land in
`builds/` inside this repo (gitignored — personal output, not source).
