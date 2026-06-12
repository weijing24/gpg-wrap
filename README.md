# gpg-wrap

A friendly wrapper around the gpg CLI.

## Usage

```
gpg-wrap [-H DIR] <command> [args]
gpg-wrap help [command]
```

## Commands

| Command | Description |
|---------|-------------|
| `create <name> <email> [opts]` | Generate a keypair (auto passphrase by default) |
| `list [pattern]` | List keys: fingerprint, uid, dates, has-secret |
| `expire <key> <when>` | Set/clear expiry on a key + all its subkeys |
| `encrypt <recipient> <file>...` | Encrypt files → `<file>.gpg` |
| `decrypt <file>...` | Decrypt files → strips `.gpg` |
| `export <key> [--secret] [-o file]` | Export public (or secret) key, armored |

## Global Options

| Option | Description |
|--------|-------------|
| `-H, --home DIR` | Use an isolated keyring dir (or set `$GPGW_HOME`) |
| `-h, --help` | Show help |

## Passphrase Handling

- Pass `--passphrase VALUE`, set `$GPGW_PASSPHRASE`, or you'll be prompted.
- Passphrases are fed to gpg via `--passphrase-fd` — never visible in `ps`.
- On `create` with no `--passphrase`, a strong 40-char passphrase is generated and printed once.

## Examples

```bash
# Create a keypair (auto passphrase, expires in 1 year)
gpg-wrap create "example" example@example.com --expire 1y

# List all keys
gpg-wrap list

# Extend expiry
gpg-wrap expire example@example.com 1y

# Encrypt files
gpg-wrap encrypt example@example.com .env accounts.json

# Decrypt files
gpg-wrap decrypt .env.gpg accounts.json.gpg

# Export public key
gpg-wrap export example@example.com -o pub.asc

# Use an isolated keyring
export GPGW_HOME=~/.gnupg-example
gpg-wrap create "my-key" me@example.com
```

## Requirements

- `gpg` on `PATH`
- `openssl` (only needed for auto passphrase generation on `create`)

## Compatibility

Works on both macOS and Linux. Date formatting auto-detects GNU (`date -d`) and
BSD/macOS (`date -r`), so expiry dates display correctly on either platform.
