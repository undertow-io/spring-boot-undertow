# Property Prefix Migration

As of the first release, all Undertow configuration properties have moved out of the
`server.*` and `management.*` namespaces reserved by Spring Boot. The old prefixes
are deprecated, still work, and will be removed in the next feature release.

## Old → New Key Table

### Server properties (`server.undertow.*` → `undertow.server.*`)

| Deprecated key | New key |
|---|---|
| `server.undertow.max-http-post-size` | `undertow.server.max-http-post-size` |
| `server.undertow.buffer-size` | `undertow.server.buffer-size` |
| `server.undertow.direct-buffers` | `undertow.server.direct-buffers` |
| `server.undertow.eager-filter-init` | `undertow.server.eager-filter-init` |
| `server.undertow.max-parameters` | `undertow.server.max-parameters` |
| `server.undertow.max-headers` | `undertow.server.max-headers` |
| `server.undertow.max-cookies` | `undertow.server.max-cookies` |
| `server.undertow.decode-slash` | `undertow.server.decode-slash` |
| `server.undertow.decode-url` | `undertow.server.decode-url` |
| `server.undertow.url-charset` | `undertow.server.url-charset` |
| `server.undertow.always-set-keep-alive` | `undertow.server.always-set-keep-alive` |
| `server.undertow.no-request-timeout` | `undertow.server.no-request-timeout` |
| `server.undertow.preserve-path-on-forward` | `undertow.server.preserve-path-on-forward` |
| `server.undertow.accesslog.enabled` | `undertow.server.accesslog.enabled` |
| `server.undertow.accesslog.pattern` | `undertow.server.accesslog.pattern` |
| `server.undertow.accesslog.prefix` | `undertow.server.accesslog.prefix` |
| `server.undertow.accesslog.suffix` | `undertow.server.accesslog.suffix` |
| `server.undertow.accesslog.dir` | `undertow.server.accesslog.dir` |
| `server.undertow.accesslog.rotate` | `undertow.server.accesslog.rotate` |
| `server.undertow.threads.io` | `undertow.server.threads.io` |
| `server.undertow.threads.worker` | `undertow.server.threads.worker` |
| `server.undertow.options.server.*` | `undertow.server.options.server.*` |
| `server.undertow.options.socket.*` | `undertow.server.options.socket.*` |

### Management properties (`management.server.undertow.*` → `undertow.management.*`)

| Deprecated key | New key |
|---|---|
| `management.server.undertow.accesslog.prefix` | `undertow.management.accesslog.prefix` |

## Behavior during deprecation period

- Old keys are automatically mapped to new keys at startup via an `EnvironmentPostProcessor`.
- If both old and new keys are set for the same property, the **new key wins**.
- A consolidated `WARN` is logged at application startup listing every deprecated key
  in use and its replacement.
- Environment variable style (`SERVER_UNDERTOW_THREADS_IO`) is also supported.

## Example migration

**Before:**
```yaml
server:
  undertow:
    threads:
      io: 4
      worker: 32
    accesslog:
      enabled: true
```

**After:**
```yaml
undertow:
  server:
    threads:
      io: 4
      worker: 32
    accesslog:
      enabled: true
```
