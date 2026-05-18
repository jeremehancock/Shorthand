# Shorthand &mdash; Custom Shortcodes for Bludit

Define a tag once, drop it anywhere in your pages and posts. Works with both the Markdown and HTML editors.

## Install

1. Download the latest version from the zips directory.
2. Uncompress the zip file.
3. Copy the plugin folder into the folder `/bl-plugins/` on the server.

## Activate
1. Go to the **Admin panel**, on the left sidebar go to **Settings > Plugins**.
2. Look for the plugin in the list and click **Activate**.

## Usage

Each shortcode has a **name** and a **template**.

| Call form                                   | Template can use                       | Example                                        |
| ------------------------------------------- | -------------------------------------- | ---------------------------------------------- |
| `[name]`                                    | plain text                             | `[year]`                                       |
| `[name foo="value" ...]`                    | `{foo}` &mdash; matches the attr name  | `[email to="hi@example.com"]`                  |
| `[name foo="value"]inner[/name]`            | `{content}` (literal) + `{foo}`        | `[callout type="warning"]Heads up![/callout]`  |

The placeholder name in the template is **whatever you call the attribute** &mdash; `{to}` pairs with `to="..."`, `{color}` pairs with `color="..."`, and so on. Only `{content}` is a fixed name (the inner text between `[tag]...[/tag]`). If a template contains `{content}`, Shorthand looks for a matching `[/name]` closing tag; otherwise it treats the shortcode as self-closing.

### Examples

| Name      | Template                                                                       | Used as                                          |
| --------- | ------------------------------------------------------------------------------ | ------------------------------------------------ |
| `year`    | `2026`                                                                         | `[year]`                                         |
| `email`   | `<a href="mailto:{to}">{to}</a>`                                               | `[email to="hi@example.com"]`                    |
| `callout` | `<div class="callout {type}">{content}</div>`                                  | `[callout type="warning"]Heads up![/callout]`    |
| `yt`      | `<iframe src="https://www.youtube.com/embed/{id}" allowfullscreen></iframe>`   | `[yt id="dQw4w9WgXcQ"]`                          |

### Notes

- Names accept letters, digits, `-`, and `_`.
- Attribute values can be wrapped in `"..."`, `'...'`, or written bare (`key=value`).
- Templates can reference other shortcodes &mdash; Shorthand runs up to three passes so nested shortcodes resolve, then stops to prevent infinite loops.
- Shortcodes are replaced **before** Markdown parsing, so a template that emits Markdown can be rendered by the Markdown parser on Markdown-source pages.

## Things to keep in mind

- **Two roles, two trust levels.** Bludit has Administrators (who can edit Shorthand templates) and Editors (who can only write pages). Attribute values typed into a page (anything inside `[tag attr="..."]`) are HTML-escaped (`htmlspecialchars`, `ENT_QUOTES`) before being plugged into the template, so an Editor account cannot inject JavaScript through a shortcode call. If you're a solo admin doing both jobs, this is defense-in-depth rather than a hard boundary &mdash; it still helps if you later add an editor, paste content from elsewhere, or another account is compromised.
- **URL-style attributes are auto-cleaned.** Any attribute named `url`, `src`, `href`, `link`, `action`, `formaction`, `poster`, or `background` only accepts safe schemes (`http`, `https`, `mailto`, `tel`, `ftp`, `ftps`, `sms`) or relative paths / fragments. `javascript:`, `data:`, `vbscript:`, and similar schemes &mdash; including newline- and tab-smuggled variants &mdash; are stripped to an empty string.
- **Don't put any placeholder inside `<script>` or `<style>`.** HTML-escaping doesn't make a value safe inside JavaScript or CSS contexts. The plugin's admin form will warn you if a template does this. Inline `on...="..."` event handlers get the same warning.
- **`{content}` is not escaped.** It's the page author's own content, so it's at the same trust level as the rest of the page. Don't wrap it in a `<script>` template.
- **Watch for Markdown link-reference collisions.** Shortcodes are replaced *before* Markdown parsing, so a shortcode named `foo` will also consume a `[foo]: https://...` link reference. Pick distinctive names if you use reference-style links.
- **Nested shortcodes are allowed, up to 3 passes.** A template can reference another shortcode, but the resolver caps at three passes to prevent infinite loops.
- **Match your template to your editor.** If you're using the **HTML editor**, your templates must emit HTML &mdash; the page content is stored as HTML (e.g. a page that contains `[mytag]` is saved as `<p>[mytag]</p>`), and the Markdown parser does not descend into block-level HTML, so a template emitting `**bold**` would show as literal text. If you're using the **Markdown editor**, templates can emit either HTML or Markdown. **HTML-emitting templates work in both editors**, so when in doubt, write HTML.
- **Shortcode names are validated** against `^[a-zA-Z0-9_-]+$` on both save and replacement, so malicious names can't break the regex.

## Compatibility

- Bludit 3.22

## AI Disclaimer

This project was created with the help of AI.