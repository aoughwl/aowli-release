# aowli — release binaries

Prebuilt distribution of the **aowli** interpreter: it runs a nimony program's
typed, semantically-analysed NIF (`.s.nif`) — the form emitted after `nimsem`.
**Binary only** — the source lives in the private `aoughwl/aowli` repo.

This repo is **public**: anyone may download and use these binaries.
**Issues welcome** → <https://github.com/aoughwl/aowli-release/issues>

## Binaries

| binary | what it does |
|--------|--------------|
| `aowli-interp` | run a program; `--trace` prints the execution call-tree |
| `aowli-dbg`    | batch breakpoints: run with `--break:LINE` / `--break-func:NAME`, dump each frame's variables at every hit |

## Run

```sh
chmod +x bin/aowli-interp bin/aowli-dbg
./bin/aowli-interp <module.s.nif>            # run the program
./bin/aowli-interp --trace <module.s.nif>    # execution call-tree on stderr
./bin/aowli-dbg  --break:29 <module.s.nif>   # break at line 29, dump frame vars
```

## Build & hardening

Built 2026-07-22 from the private source via the `aowl-release` pipeline:

- **licence gate** — fail-closed module-init check
- **`strip --strip-all`** — symbol table removed (no proc/type names)

Verified before publishing: the stripped binaries expose **no aowli source paths
and no internal interpreter/debugger proc/type names**. Residual strings are
upstream only (nimony stdlib assertion paths, the mimalloc allocator).

## Integrity

| binary | size (bytes) | sha256 |
|--------|-------------|--------|
| `bin/aowli-interp` | 720,800 | `63329b10fbfec6ea53d97bd933de8d7c957f0454f9ca53c61755b7d6d998622a` |
| `bin/aowli-dbg`    | 683,936 | `461c90c93f9eb608bbda9fd1b8bc9cabe450bfad3812ef3d8a96528ab9ae1086` |

```sh
sha256sum -c <<'EOF'
63329b10fbfec6ea53d97bd933de8d7c957f0454f9ca53c61755b7d6d998622a  bin/aowli-interp
461c90c93f9eb608bbda9fd1b8bc9cabe450bfad3812ef3d8a96528ab9ae1086  bin/aowli-dbg
EOF
```

## VirusTotal

Look up each build by hash:

- aowli-interp — <https://www.virustotal.com/gui/file/63329b10fbfec6ea53d97bd933de8d7c957f0454f9ca53c61755b7d6d998622a>
- aowli-dbg — <https://www.virustotal.com/gui/file/461c90c93f9eb608bbda9fd1b8bc9cabe450bfad3812ef3d8a96528ab9ae1086>

(Scan results populate once a binary is submitted to VirusTotal.)
