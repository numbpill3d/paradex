# paradex

a small vertical retro-mac terminal widget for cataloguing the aesthetics,
moods, and motifs you keep coming back to — then pulling a random set from
that catalogue to seed a new project. chrome has a subtle bevel — crisp
top/left border, soft `░`-shaded bottom/right — no color, just glyphs.

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

on launch (when the catalogue isn't empty) it also surfaces one entry
unprompted — "today's pull," picked deterministically from the date so it
stays the same across relaunches within a day, above the usual full list.

## commands

```
add <category> <text> [#tag ...]        file a new entry
edit <id> <text>                        replace an entry's text
list [category|#tag|@project]  (ls)     show entries, optionally filtered
tag <id> #tag [#tag ...]                add tags to an entry
untag <id> #tag [#tag ...]              remove tags from an entry
mv <id> <category>                      recategorize an entry
rm <id>  (del)                          remove an entry
link <id1> <id2>                        mark two entries as compatible
unlink <id1> <id2>                      remove a link
clash <id1> <id2>                       mark two entries as conflicting
unclash <id1> <id2>                     remove a clash
roll [n] [category|#tag|@project]       randomly pull n entries (default 3)
roll spread <name>                      one guaranteed pull per spread category
spread <name> <cat> [cat ...]           define a named set of categories
spreads                                 list saved spreads
build [name]                            save the last roll as a build sheet
export [category|#tag|@project]         dump matching entries to builds/
newproj <name>                          start a tracked project
projects [id]                           list projects, or show one in detail
pin <project-id> <id> [id ...]          pin entries to a project
unpin <project-id> <id> [id ...]        unpin entries from a project
status <project-id> <stage>             seed | active | shelved | done
note <project-id> <text>                set a project's notes
rmproj <project-id>                     delete a project (not its entries)
categories  (cat)                       list categories + counts
tags                                     list tags + counts
stats                                    catalogue summary
find <term>                             search text/tags/category
clear                                    drop the active filter
undo                                     revert the last change
help                                     command reference
quit  (:q, exit)                        leave
```

## example

```
add palette black voidscape, red neon veins #hackercore #ritual
add mood restless midnight circuitry #glitch
add mechanic feed a wraith fragments of stolen uptime #zahl #idle
edit 3 feed a wraith fragments of stolen focus-time
mv 3 mechanic
link 1 2
roll 3
build my-next-thing
```

`build` writes a plain-text seed sheet to `builds/<name>.txt` grouping the
rolled entries by category, with a blank line at the bottom to fill in what
you're building. `export` writes the same style of sheet but for the full
catalogue (or a filtered slice of it) instead of the last roll — no blank
seed line, since it's a dump/backup rather than a concept prompt.

`undo` reverts a single last change (any mutating command) — one level
only, no multi-step history.

### relationships

`link`/`unlink` mark two entries as compatible; `clash`/`unclash` mark them
as conflicting. `roll` (both plain and `roll spread`) respects both: it
avoids pairing clashing entries in the same pull and leans toward pulling
things already linked to what's been picked so far.

### spreads

a spread is a named list of categories. `spread concept mood palette
texture mechanic` saves one; `roll spread concept` then pulls exactly one
entry per category instead of grabbing n random entries from the whole
pool — a guaranteed-coverage "recipe" roll instead of a grab-bag. feeds
`last_roll` the same as a plain roll, so `build`/`export` work after it
same as always.

### projects

`newproj <name>` starts a tracked project (status starts at `seed`).
`pin`/`unpin` attach or detach catalogue entries, `status` moves it through
`seed -> active -> shelved -> done`, `note` holds free text. `projects`
alone lists all of them; `projects <id>` shows one in full (status, notes,
pinned entries). Any command that takes a `category|#tag` filter — `list`,
`roll`, `export` — also accepts `@<project-id>` or `@<name-substring>` to
scope to that project's pinned entries.

## storage

entries, projects, and spreads all persist to `~/.local/share/paradex/db.json`.
build sheets land in `builds/` inside this repo (gitignored — personal
output, not source).
