# aowli — release binaries

Prebuilt distribution of the **aowli** interpreter: it runs a nimony program's
typed, semantically-analysed NIF (`.s.nif`) — the form emitted after `nimsem`.
**Binary only** — the source lives in the private `aoughwl/aowli` repo.

This repo is **public**: anyone may download and use these binaries.
**Issues welcome** → <https://github.com/aoughwl/aowli-release/issues>

## Binaries

| binary | what it does |
|--------|--------------|
| `aowli-interp` | run a program; `--trace` prints the call-tree, `--trace-depth:N` bounds it, `--trace-profile` gives per-routine call counts |
| `aowli-dbg`    | batch breakpoints: `--break:LINE` / `--break-func:NAME`, dumps each frame's locals at every hit (bounded render; caps at 20k hits) |

## Run

```sh
chmod +x bin/aowli-interp bin/aowli-dbg
./bin/aowli-interp <module.s.nif>              # run the program
./bin/aowli-interp --trace-profile <m.s.nif>   # per-routine call counts (wide programs)
./bin/aowli-dbg  --break:29 <module.s.nif>     # break at line 29, dump frame vars
```

## Build & hardening

Built from the private source via the `aowl-release` pipeline:

- **IR control-flow obfuscation** (`obfnif`, typed-NIF layer — opaque predicates, dead guards, junk) — behaviour-preserving, applied before codegen so it reaches the machine code
- **licence gate** — fail-closed module-init check
- **`strip --strip-all`** — symbol table removed (no proc/type names)

Verified before publishing: identical program output vs the source build, and the
stripped binaries expose **no aowli source paths and no internal proc/type names**.

## Integrity

| binary | size (bytes) | sha256 |
|--------|-------------|--------|
| `bin/aowli-interp` | 728,992 | `5ed7c80eade6800826caece1552fccfef6e69b9aa262d3ba744699613a46568f` |
| `bin/aowli-dbg`    | 692,128 | `3a65ee45251fc9ca0ebb55973352be1244892aabaf0ca9d4beb5c8831291034b` |

```sh
sha256sum -c <<'EOF'
5ed7c80eade6800826caece1552fccfef6e69b9aa262d3ba744699613a46568f  bin/aowli-interp
3a65ee45251fc9ca0ebb55973352be1244892aabaf0ca9d4beb5c8831291034b  bin/aowli-dbg
EOF
```

## VirusTotal

Look up each build by hash:

- aowli-interp — <https://www.virustotal.com/gui/file/5ed7c80eade6800826caece1552fccfef6e69b9aa262d3ba744699613a46568f>
- aowli-dbg — <https://www.virustotal.com/gui/file/3a65ee45251fc9ca0ebb55973352be1244892aabaf0ca9d4beb5c8831291034b>

(Scan results populate once a binary is submitted to VirusTotal.)
