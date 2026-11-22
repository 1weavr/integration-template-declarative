# Declarative integration template

A 1weavr integration with **no code**: the requests it makes and how their answers are read all live in
`manifest.json`. Pick it for a tool with a plain REST API, such as an issue tracker.

This one turns a capture into an issue in your tool, lets people pick the project, and can mark the capture fixed
when the issue is done.

## Status

This template is ahead of the platform. Read this before you build on it:

- **The declarative runtime is not available yet.** Today 1weavr runs only its built-in integrations. If you upload this
  integration now, every call answers "The declarative runtime is not available on this server yet". Its manifest and test
  cases are checked, but nothing runs.
- **There is no command-line tool yet** to run the test cases on your machine. They will run in 1weavr's developer
  console once the runtime is there.
- **Uploading packages is not built yet** either. Watch this repository: this README changes as each of these ships.

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
