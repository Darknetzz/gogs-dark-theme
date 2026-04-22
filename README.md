# Gogs Dark Theme

A drop-in dark theme for [Gogs](https://gogs.io), injected through Gogs' built-in
[custom templates](https://gogs.io/advancing/custom-templates) mechanism. No
rebuild, no fork — just two files.

## Preview

Covers the full site: dashboard, issues/PRs, repository browser, diff view,
source-file view (highlight.js), Markdown rendering, admin panel, settings,
login, and explore pages. All colors read from CSS variables so retheming
means editing one block.

## Installation

### Option A — clone directly into your Gogs `custom` folder

```bash
# Wherever GOGS_CUSTOM (or /data/gogs for the official Docker image) points
cd /path/to/gogs/custom
git clone git@github.com:Darknetzz/gogs-dark-theme.git .
```

### Option B — copy the files in

```bash
git clone git@github.com:Darknetzz/gogs-dark-theme.git
cp -r gogs-dark-theme/public gogs-dark-theme/templates /path/to/gogs/custom/
```

### Option C — Docker with a bind mount

```yaml
services:
  gogs:
    image: gogs/gogs
    volumes:
      - gogs-data:/data
      - ./gogs-dark-theme:/data/gogs/custom:ro   # or merge with your own custom/
```

### Option D — Docker with `docker cp` (matches the upstream image layout)

The official image keeps `/data/gogs` on a named volume. If you don't want a
bind mount, copy the two files straight into the running container:

```bash
docker cp public/css/dark-mode.css gogs:/data/gogs/public/css/dark-mode.css
docker cp templates/inject/head.tmpl gogs:/data/gogs/templates/inject/head.tmpl
docker exec gogs chown -R git:git /data/gogs/public /data/gogs/templates
```

No restart is required — Gogs serves both files on the next page load.

## How it works

`templates/inject/head.tmpl` is loaded by Gogs on **every page** via its
`inject/head.tmpl` extension point. It injects two tags:

```html
<link rel="stylesheet" href="{{AppSubURL}}/css/dark-mode.css">
<meta name="color-scheme" content="dark">
```

That stylesheet — `public/css/dark-mode.css` — gets served by Gogs' static
file handler at `/css/dark-mode.css` and overrides the bundled Semantic UI
2.4 + `gogs.min.css` rules.

## Customizing colors

All colors are driven by CSS variables at the top of `dark-mode.css`:

```css
:root {
    --dm-bg:            #0d1117;   /* page background */
    --dm-bg-alt:        #161b22;   /* cards, menus, segments */
    --dm-bg-raised:     #1c2128;   /* hover, active rows, code */
    --dm-bg-input:      #0d1117;   /* form inputs */
    --dm-border:        #30363d;
    --dm-text:          #c9d1d9;
    --dm-text-muted:    #8b949e;
    --dm-text-strong:   #f0f6fc;
    --dm-link:          #58a6ff;
    --dm-primary:       #238636;   /* primary / success buttons */
    --dm-danger:        #da3633;
    --dm-warning:       #d29922;
    /* ... */
}
```

Tweak those and the whole UI follows. The default palette is GitHub-dark-ish.

## Coverage

- Top navigation, user menu, language picker
- Dashboard (feed, sidebar, activity heatmap)
- Issues / Pull Requests (list, filters, counter buttons, new-issue form)
- Repository browser (file list, file view, source highlighting)
- Commit list, commit view, diff view (add/del/tag lines)
- Markdown rendering (headings, tables, lists, task checkboxes, blockquotes,
  horizontal rules, fenced code blocks)
- Syntax highlighting — both server-side (Chroma) and client-side
  (highlight.js 9.18 in source file view) get a GitHub-dark palette
- Admin panel (dashboard, users, repos, config, monitoring)
- User settings (profile, password, email, SSH keys, security, applications)
- Repository settings (options, collaboration, branches, webhooks, hooks)
- Login, register, forgot-password, explore (repos/users)
- Footer, native `<input type="file">` button, checkboxes/radios

## Compatibility

Tested against Gogs `next-latest` (tracks mainline). The selectors target
stable Semantic UI 2.4 class names Gogs has shipped since ~0.12, so older
releases should work too. Open an issue if a page renders with
low-contrast elements.

## License

MIT — see [LICENSE](LICENSE).
