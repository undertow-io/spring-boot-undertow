# Changelog

## Unreleased

### Breaking changes (with backward compatibility)

- **Configuration property namespace migration (#3):** All Undertow properties have
  moved from `server.undertow.*` / `management.server.undertow.*` to
  `undertow.server.*` / `undertow.management.*`. The old prefixes are deprecated,
  still work via an automatic mapping shim, and will be removed in the next feature
  release. A `WARN` is logged at startup listing any deprecated keys in use.
  See [MIGRATION.md](MIGRATION.md) for the full old-to-new key table.
