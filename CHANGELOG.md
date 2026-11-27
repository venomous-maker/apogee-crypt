# Changelog

Every release of apogee-crypt, newest first. Each is a `vX.Y.Z` tag with a GitHub
release carrying its section; the `grav` line in `grav.toml` says which
compiler versions it builds with.

## Unreleased

### Fixes

- A Redis connection shared by several threads (the cache, session and queue-dashboard stores, used by a multi-threaded server or queue worker) no longer mixes up replies or stalls: every command is one locked round trip (grav-redis 0.2.2). Uses the new releases of apogee-package.

## 0.2.1 (2026-11-27)

### Fixes

- Database connections that the server dropped while idle (MySQL's `wait_timeout`, a restart, a proxy's idle limit) reconnect on their next use instead of failing with "server has gone away" (grav-db 0.2.1). Uses the new releases of apogee-package.

## 0.2.0 (2026-11-25)

Needs grav 1.12.0 or newer.

- `CryptPackage`: the module connects itself to an apogee-framework app (apogee-package):
  its settings, wiring moved here from the framework.

### Dependencies

- Uses apogee-package 0.1.0 (from their GitHub tags).

## 0.1.0 (2026-11-20)

The first release. Needs grav 1.10.0 or newer.

- `Encrypter` and the `Crypt` facade: text and JSON-plain values sealed with
  ChaCha20 and HMAC-SHA256 (encrypt-then-MAC), key rotation through previous
  keys, `Crypt.generateKey()`.
- `MessageSigner`: messages signed for a purpose, optionally until a deadline.
- `ChaCha20.xor`, the stream cipher itself.
