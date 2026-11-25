# Changelog

Every release of apogee-crypt, newest first. Each is a `vX.Y.Z` tag with a GitHub
release carrying its section; the `grav` line in `grav.toml` says which
compiler versions it builds with.

## Unreleased

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
