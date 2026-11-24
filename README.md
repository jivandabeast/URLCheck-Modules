# URLCheck-Modules

Community custom modules for [URLCheck](https://github.com/jivandabeast/URLCheck), a fork of
[TrianguloY/URLCheck](https://github.com/TrianguloY/URLCheck) that supports user-defined modules.

A custom module is a JSON file that URLCheck loads as its own module. It can rewrite URLs,
send them to a service, or fetch and show information about them, with no app changes needed.

## Installing a module

1. In URLCheck, open **Modules** and enable **Custom modules**.
2. Tap **Import from URL** and paste the *raw* link of a module, for example:
   ```
   https://raw.githubusercontent.com/jivandabeast/URLCheck-Modules/main/modules/wayback.json
   ```
3. Review the confirmation dialog. It lists the servers each module sends URLs to.
4. Enable the new module in the list.

Some modules need your own settings (an instance URL or an API token). Edit them after importing
with **Custom modules → Json editor**.

## Modules

| Module | What it does | Sends URLs to |
|--------|--------------|---------------|
| [wayback.json](modules/wayback.json) | Shows the latest Wayback Machine snapshot and can open it | archive.org |
| [reddit-redlib.json](modules/reddit-redlib.json) | Rewrites Reddit links to a Redlib instance | — |
| [linkding.json](modules/linkding.json) | Bookmarks the URL in your Linkding instance | your Linkding server |
| [page-title.json](modules/page-title.json) | Downloads the page and shows its title (button only) | the page itself |

## Writing a module

The full format is documented in [docs/format.md](docs/format.md).

## Contributing

- One module per file in `modules/`, named after the module (`my-module.json`).
- The file must be valid JSON with at least `name` and `actions`.
- Never include real API tokens, passwords or personal instance URLs. Use placeholders like `YOUR_TOKEN` and `example.org`, and explain them in the `description`.
- Add a row to the table above, including every server the module contacts.
- Prefer buttons over `"automatic": true` for `request` actions. An automatic request contacts a server for every matching URL the user opens.
