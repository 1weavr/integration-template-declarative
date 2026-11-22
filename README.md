# Declarative integration template

Files a capture as an issue in your tool, and marks the capture fixed when your tool says the issue is done. There is
no code: each call below is answered by a request template and a response mapping in `manifest.json`.

## The calls it answers

| Call | Answered by | Input you map from | Output you must produce | Limit |
|---|---|---|---|---|
| `connection.verify` | `connection.verify` → `actions.whoAmI` | nothing; the connection's fields (`site`, `email`, `api_token`) fill the request | `{ account }`, shown as "Connected as …" | 10 s, no retry |
| `lookup` | `lookups.projects`, `issueTypes`, `people`, `labels` | `{ lookup, query, cursor, target?, values }` | `{ items: [{ value, label }], next }` | 5 s, no retry |
| `send.file` | `contributes["item.actions"].file` → `actions.createIssue` | `{ target, report, markdown, outcome, values, marker }`: `values` holds the form (`title`, `type`, `assignee`, `labels`) | `{ outcome: "issue", target, ref: { scheme: "key", key }, url }` | 120 s, never retried |
| `send.find` | `contributes["item.actions"].find` → `actions.findByMarker` | `{ target, marker, outcome, since }` | `{ found: true, outcome, target, ref, url }` or `{ found: false }` | 120 s, no retry |
| `inbound.handle` | `events.in.handle` → `actions.onToolEvent` | `{ headers, body }`, after the `X-Tool-Signature` HMAC check | `{}`, with effects `filing.state` (the issue's state, shown on the capture) and `item.fixed` (when the issue is done and `setup.close_fixes` is on) | 10 s, 3 retries |

`send.file` is never retried, because a second try could file twice. When its outcome is unknown, 1weavr calls
`send.find` with the same `marker` to learn whether the first try landed. The marker is the last line of `markdown`
(`Ref <marker>`), so `createIssue` sends `markdown` as the issue body and `findByMarker` searches for it.

Every call carries `protocol`, `call`, a unique `delivery` id, the `integration`, the `installation` and its
`input`. The answer repeats `call` and `delivery` and is either `{ ok: true, output, effects? }` or
`{ ok: false, error: { code, message } }`, where `code` is `refused`, `failed`, `unauthenticated` or `rate_limited` and
`message` is shown to people as written. An answer that does not match its call's shape is refused and logged.

## Security

### What 1weavr enforces

- **Hosts.** A request reaches only a host listed in `hosts`, and only for the uses listed there (`fetch`, `link`,
  `auth`). Anything else is blocked before it leaves, and the block is recorded in the installation's Log.
- **Secrets.** A secret enters a request only through a `${secret.<name>}` hole that 1weavr fills, and only for a host
  that names that secret under `secrets`; in a body, only on an `auth` host. The manifest names secrets; it never holds them.
- **Masking.** Secret values are masked in the Log and the developer console, in headers and in bodies.
- **Consent.** Admins approve the permissions and hosts the manifest asks for when they install. A version that adds a
  permission, a host, a secret, an event or an effect is a major version, and admins approve it before it runs.
- **Publisher secrets** (`${secret.publisher.<name>}`) are sealed in your integration's Environments, Test and Live
  apart, and are never shown again once saved.

### What you are responsible for

- **Ask for the least.** List only the hosts, uses and permissions the requests need. Keep hosts exact
  (`{site}.your-tool.example`), never a broad wildcard.
- **Keep the webhook secret.** `events.in.verify` checks every delivery from your tool against `webhook_secret` with
  HMAC-SHA256 over the raw body. Each installation sets its own; never ship one in the repository.
- **No real secrets in the repository.** Test cases use stand-in values (`fixture-token`), never a real token.
- **Words people read.** `error.message` is shown to people as written: never put a token, a URL with a key, or an
  internal detail in it.
- **One send, one issue.** `send.file` is never retried; keep the marker in what you create so `send.find` can find an
  earlier try instead of filing twice.

Report a vulnerability privately to security@1weavr.com (see `SECURITY.md`).

## What is in it

```
manifest.json   what it is, the hosts it may call, how people connect it, and the requests
icon.svg        its icon in the catalogue (square, flat colour)
fixtures/       test cases: a call, what goes in, the replies your tool gives, and the answer it must give
```

## Make it yours

1. Press **Use this template** and make your own repository.
2. In `manifest.json`, change `id`, `name`, `summary`, `description` and `links`, and replace `your-tool.example`
   with your tool's real host everywhere, including the test cases.
3. Fill in the requests for the calls you need, and add a test case to `fixtures/` for each one.
4. Before it can be listed publicly, `links.privacy`, `links.terms` and a description of 200 characters or more
   are required.

The full guide, with the manifest, test cases and publishing: https://1weavr.com/docs/build-an-integration

## Questions

Email support@1weavr.com, or open an issue on this repository. Security reports go to security@1weavr.com.

## Licence

MIT: copy it, change it and ship it as your own. See `LICENSE`.
