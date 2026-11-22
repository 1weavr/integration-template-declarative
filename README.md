# Declarative integration template

Turns a capture into an issue in your tool, with no code: every request it makes and how each answer is read live in
`manifest.json`. Start here for a tool with a plain REST API, such as an issue tracker.

What it does in 1weavr, and which part of the manifest does it:

| Where | What people see | In `manifest.json` |
|---|---|---|
| Set-up | Connect with a site name, an account email and an API token; the token is checked by asking who it belongs to | `connection` (`token-host`), `actions.whoAmI` |
| Send to, in the app and the extension | A form: project, title, type, assignee and labels, each list filled from your tool | `contributes["item.actions"]`, `lookups.projects`, `issueTypes`, `people`, `labels` |
| Sending | Creates the issue; on a retry, finds the issue an earlier try made instead of making a second | `actions.createIssue`, `actions.findByMarker` |
| Sent to, and list rows | The issue's key and link on the capture, and its status on the list | `contributes["item.sent"]`, `contributes["list.status"]` |
| Project default | Each 1weavr project can send to one project in your tool by default | `contributes["project.destination"]` |
| Webhook from your tool | When the issue is done, the capture is marked fixed (a switch in set-up) and a line is added to its activity | `events.in`, `actions.onToolEvent`, `setup.close_fixes` |

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

Email support@1weavr.com, or open an issue on this repository.

## Licence

MIT: copy it, change it and ship it as your own. See `LICENSE`.
