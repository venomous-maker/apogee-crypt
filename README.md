# apogee-crypt

Encryption and signing for [Apogee](https://github.com/venomous-maker): values
sealed under the app's key, so no one without it can read or alter them, and
messages signed so they can be handed out (reset links, invite tokens) and
trusted when they come back, optionally until a deadline. No dependencies
beyond Grav's standard library, and no OpenSSL.

```typescript
import "std"
import "apogee-crypt"
use apogee.*

Crypt.setKey(env("APP_KEY"))                         // "base64:..." from Crypt.generateKey()
let sealed = Crypt.encrypt(cart)                     // any JSON-plain value
let back = Crypt.decrypt(sealed) as HashMap<string, any>

let signer = new MessageSigner(env("APP_KEY"))
let token = signer.sign("user:42", "password-reset", 3600)   // valid for an hour
let who = signer.unsign(token, "password-reset")             // "user:42", or InvalidSignature
```

## In an apogee-framework app

Importing the module is all it takes: `CryptPackage` (apogee-package) connects it when
the app is created. Crypt uses the app key (`APP_KEY`), and a `MessageSigner` is bound in the container. A key too short to encrypt with does not stop the app; Crypt says why when first used.

| Setting | Default |
|---|---|
| `app.previous_keys` | `APP_PREVIOUS_KEYS`: older keys, comma-separated, still read |

Your own configuration wins over these defaults.

## Encrypting

| | |
|---|---|
| `new Encrypter(key, previousKeys = [])` | `key`: `"base64:"` and 32 random bytes, or any text of 32 characters or more; `previousKeys` still open what they sealed (key rotation) |
| `encryptString(text)` / `decryptString(payload)` | text in, `"v1.<data>.<mac>"` out (URL-safe: fits in a cookie, a URL or JSON) |
| `encrypt(value)` / `decrypt(payload)` | null, bool, numbers, strings, `DateTime` (as its text) and `Array`/`HashMap` of those |
| `Crypt.setKey(key, previousKeys)`, `Crypt.encrypt(...)`, ... | the app's encrypter, as statics |
| `Crypt.generateKey()` | a new random key |

A payload that was changed, made with another key, or is not a payload at all
throws `DecryptException`.

How: ChaCha20 (RFC 8439, checked against its test vector) with a random
96-bit nonce per payload, then HMAC-SHA256 over the result (encrypt-then-MAC).
The encryption and MAC keys are derived from the app key with HMAC, so the one
key never does both jobs.

## Signing

| | |
|---|---|
| `new MessageSigner(key, previousKeys = [])` | |
| `sign(message, purpose = "", ttlSeconds = 0)` | `"<message>.<expires>.<signature>"`; the message can be read but not changed |
| `unsign(signed, purpose = "")` | the message; `InvalidSignature` (with `expired` set when that is why) otherwise |
| `isValid(signed, purpose = "")` | |

The purpose is part of what is signed: a token made for `"invite"` does not
pass as a `"password-reset"` one.

## Tests

```bash
grav test tests/crypt.grav
grav test tests/package.grav
```
