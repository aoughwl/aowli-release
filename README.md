# aowli — release binary

Prebuilt distribution of the **aowli** interpreter: it runs a nimony program's
typed, semantically-analysed NIF (`.s.nif`) — the form emitted after `nimsem`.
**Binary only** — the source lives in the private `aoughwl/aowli` repo.

## Run

```sh
chmod +x bin/aowli-interp
./bin/aowli-interp <module.s.nif>            # run the program
./bin/aowli-interp --trace <module.s.nif>    # execution call-tree on stderr
```

## Build & hardening

Built 2026-07-22 from the private source via the `aowl-release` pipeline:

- **licence gate** — fail-closed module-init check
- **`strip --strip-all`** — symbol table removed (no proc/type names)

Verified before publishing: the stripped binary exposes **no aowli source paths and
no internal interpreter proc/type names**. Residual strings are upstream only
(nimony stdlib assertion paths, the mimalloc allocator).

## Integrity

| field  | value |
|--------|-------|
| file   | `bin/aowli-interp` |
| size   | 720,800 bytes |
| sha256 | `63329b10fbfec6ea53d97bd933de8d7c957f0454f9ca53c61755b7d6d998622a` |

```sh
sha256sum -c <<< '63329b10fbfec6ea53d97bd933de8d7c957f0454f9ca53c61755b7d6d998622a  bin/aowli-interp'
```

## VirusTotal

Look up this build by hash:
<https://www.virustotal.com/gui/file/63329b10fbfec6ea53d97bd933de8d7c957f0454f9ca53c61755b7d6d998622a>

(Scan results populate once the binary is submitted to VirusTotal.)
