# Custom module format

Custom modules let you extend URLCheck without changing the app: each one is a small JSON definition
that shows up in the modules list like any built-in module (it can be enabled, disabled, reordered and decorated).

## Enabling

1. Open **Modules** and enable the **Custom modules** module. While it is disabled, no custom module is loaded.
2. Use **Json editor** to write modules by hand, or **Import from URL** to download one (or a whole catalog) from a community repository.
3. Each custom module now appears in the list as its own module.

> ⚠ `request` actions send data (usually the URL you are checking) to the server they point at.
> The import dialog and each module's configuration list those servers. Only import modules you trust.

## Format

The editor holds an object where each key is a module id and each value a module definition:

```json
{
  "my-module": {
    "name": "My module",
    "description": "Shown in the module configuration",
    "enabled": true,
    "regex": "^https?://(www\\.)?example\\.com/",
    "excludeRegex": "/login",
    "actions": [ ... ]
  }
}
```

| Field          | Required | Description                                                                                   |
|----------------|----------|-----------------------------------------------------------------------------------------------|
| `name`         | yes      | Name shown in the modules list and the module title.                                          |
| `actions`      | yes      | List of actions (see below).                                                                  |
| `description`  | no       | Text shown in the module configuration.                                                       |
| `enabled`      | no       | Whether the module is enabled when first added. Default `true`.                               |
| `regex`        | no       | String or list of strings. The module only appears if at least one matches the URL.          |
| `excludeRegex` | no       | String or list of strings. The module does not appear if any of them matches the URL.         |

When imported from a URL, the file can be either a full catalog (as above) or a single module definition
(an object with `name` and `actions`); in the latter case its id is generated from the name.

### Templates

URL-like and text fields of actions are templates, where these tokens are replaced:

| Token           | Value                                                                  |
|-----------------|------------------------------------------------------------------------|
| `$URL$`         | The current URL.                                                       |
| `$URL_ENCODED$` | The current URL, percent-encoded (to use as a query parameter).        |
| `$HOST$`        | The host of the URL (`www.example.com`).                               |
| `$TIMESTAMP$`   | Current time in UTC, ISO 8601.                                          |
| `$REFERRER$`    | Package of the app that opened the URL, if known.                      |
| `$0` … `$9`     | Groups of the action `regex` (`$0` is the whole match).                |

### Common action fields

| Field       | Description                                                                                      |
|-------------|--------------------------------------------------------------------------------------------------|
| `type`      | `replace` or `request`.                                                                          |
| `label`     | Button text.                                                                                     |
| `regex`     | Optional, string or list. The action only appears if it matches; its groups are `$0`…`$9`.      |
| `excludeRegex` | Optional, string or list. The action does not appear if any matches.                          |
| `automatic` | Run the action without pressing the button. Default `false`.                                     |

### `replace` action — rewrite the URL

| Field         | Description                                                         |
|---------------|---------------------------------------------------------------------|
| `replacement` | Template of the **full** new URL.                                   |

Automatic replacements are applied at most once each per URL, to avoid loops.

### `request` action — send the URL to a service / show information

| Field          | Description                                                                                                |
|----------------|------------------------------------------------------------------------------------------------------------|
| `url`          | Template of the URL to request.                                                                            |
| `method`       | `GET`, `POST`, `PUT`, `DELETE` or `HEAD`. Default `POST` if there is a `body`, `GET` otherwise.            |
| `headers`      | Object of header → template. Useful for API tokens.                                                        |
| `body`         | Template of the body. `Content-Type` defaults to JSON if it starts with `{` or `[` (override in `headers`). |
| `extract`      | Dotted path into a JSON response, like `data.items.0.title`.                                               |
| `extractRegex` | Regex applied to the response (or to the `extract` result). Group 1 is used if present, the full match otherwise. |
| `display`      | Template of the text to show, where `$RESULT$` is the extracted value. Default: the extracted value.       |
| `setUrl`       | If `true`, the extracted value replaces the current URL instead of being shown.                            |

Without `extract`/`extractRegex` only the HTTP status is shown (green on 2xx, red otherwise).
An automatic request runs every time a new URL is shown, so use it with care: it contacts the server for every matching URL.

## Examples

Every file in [`modules/`](../modules) is a working example:

| Module | Shows |
|--------|-------|
| [reddit-redlib.json](../modules/reddit-redlib.json) | `replace` with regex groups (rewrite to a privacy frontend) |
| [linkding.json](../modules/linkding.json) | `request` with `POST`, headers and a JSON body (send to your own service) |
| [wayback.json](../modules/wayback.json) | `request` with `extract`, `display` and `setUrl` (show info / change the URL) |
| [page-title.json](../modules/page-title.json) | `request` with `extractRegex` on an HTML page |

## Sharing modules

Host the JSON file anywhere it is reachable as plain text (for example a raw GitHub URL)
and import it from **Custom modules → Import from URL**. Don't share modules with your own tokens in them!
