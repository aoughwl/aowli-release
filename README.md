# aowli — release binaries

Prebuilt distribution of the **aowli** interpreter: it runs a nimony program's
typed, semantically-analysed NIF (`.s.nif`) — the form emitted after `nimsem`.
**Binary only** — the source lives in the private `aoughwl/aowli` repo.

This repo is **public**: anyone may download and use these binaries.
**Issues welcome** → <https://github.com/aoughwl/aowli-release/issues>

## v0.2.0 — runtime-complete

This release ships the **runtime-complete** aowli. All six runtime layers are now
implemented, so programs that exercise real memory, the OS, finalization, dynamic
dispatch, concurrency, and parallelism run — no longer just the pure-value subset:

| layer | what landed |
|-------|-------------|
| **flat memory** | a real flat byte heap under the value model — `cast`, `ptr`/`UncheckedArray`, `copyMem`/`equalMem`, pointer arithmetic write through actual bytes |
| **OS / fd** | genuine file descriptors and syscalls — `readFile`/`writeFile` are byte-exact, argv/env, stdio on real fds |
| **finalization + refcount** | ARC-style reference counting with destructors/finalizers firing at scope exit; custom `=destroy` hooks run |
| **loud dispatch** | method/dispatch on nil or an unimplemented shape is a **hard error**, never a silent no-op — the old silent-nil-dispatch class is gone |
| **async** | the cooperative Future/await dispatcher runs; combinators and `{.async.}` sugar execute |
| **threads** | a cooperative thread pool with `parfor` (`||` fork-join) |

Corpus parity (differential vs the nimony compiler as reference):
**tree-walker 432/469 = 92%**, **358 programs PASS-BOTH** engines, **zero
in-scope silent failures**.

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

- **licence gate** — fail-closed module-init check (build refuses to run past its validity window)
- **IR control-flow obfuscation (obfnif)** — behaviour-preserving dead-guards, junk discards, and opaque predicates woven into the typed NIF before the backend runs, so the shipped machine code carries un-elided decoy control flow (symbol rename left off; program output is unchanged)
- **`strip --strip-all`** — symbol table removed (no proc/type names)

Verified before publishing: identical program output vs the source build, and the
stripped binaries expose **no aowli source paths and no internal proc/type names**.

## Integrity

| binary | size (bytes) | sha256 |
|--------|-------------|--------|
| `bin/aowli-interp` | 1,949,600 | `72bb65fd0d3f61166d2623a3a6384dc0c75eb4bfe6b4de017d722f6d38491380` |
| `bin/aowli-dbg`    | 794,528 | `567fe7f32d8be4ee5ff40283bfad4ff794acec2bd081cc9de9c88ec52baaa123` |

```sh
sha256sum -c <<'EOF'
72bb65fd0d3f61166d2623a3a6384dc0c75eb4bfe6b4de017d722f6d38491380  bin/aowli-interp
567fe7f32d8be4ee5ff40283bfad4ff794acec2bd081cc9de9c88ec52baaa123  bin/aowli-dbg
EOF
```

## VirusTotal

Look up each build by hash:

- aowli-interp — <https://www.virustotal.com/gui/file/72bb65fd0d3f61166d2623a3a6384dc0c75eb4bfe6b4de017d722f6d38491380>
- aowli-dbg — <https://www.virustotal.com/gui/file/567fe7f32d8be4ee5ff40283bfad4ff794acec2bd081cc9de9c88ec52baaa123>

(Scan results populate once a binary is submitted to VirusTotal.)
