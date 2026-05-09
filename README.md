# dms-jira

A [DankMaterialShell](https://github.com/AvengeMedia/DankMaterialShell) plugin
that surfaces your assigned Jira Cloud tickets on the DankBar.

## Status

Pre-alpha. Scaffold only — see [DESIGN.md](./DESIGN.md) for the v1 plan.

## Features (planned for v1)

- DankBar pill showing assigned ticket count + active ticket key
- Right-click pill to pin a specific ticket as the active one
- Popout list of assigned tickets, click to open in browser
- Per-ticket actions: transition status, add comment, copy branch name
- Optional notifications: new assignments, @mentions on your open tickets

## Install

Clone into the DMS plugins directory and enable in Settings → Plugins.

```sh
git clone https://github.com/<you>/dms-jira ~/.config/DankMaterialShell/plugins/dms-jira
dms restart
```

Or symlink for development:

```sh
ln -s ~/projects/dms-jira ~/.config/DankMaterialShell/plugins/dms-jira
dms ipc call plugins reload dmsJira
```

## Configure

In Settings → Plugins → Jira Tickets:

- **Site URL** — `https://<your-org>.atlassian.net`
- **Email** — the email on your Atlassian account (top-right of
  <https://id.atlassian.com/manage-profile/security/api-tokens>)
- **Token source** — `file` (default) or `libsecret`
- **Token path** — for `file`, path to a 0600 file containing the API
  token from <https://id.atlassian.com/manage-profile/security/api-tokens>
- **JQL** — override the default ticket query
- **Poll interval** — minutes between fetches (default 5)

### Storing the token in libsecret

Recommended if you have `gnome-keyring`, `kwallet` (with libsecret
bridge), or any other Secret Service provider running. Requires the
`secret-tool` CLI (`libsecret-tools` on Debian/Ubuntu, `libsecret` on
Arch and NixOS).

One-time store:

```sh
secret-tool store --label='dms-jira API token' \
    service dms-jira account you@example.com
```

`secret-tool` prompts for the token, then the keyring stores it. Then
in plugin settings switch **Token source** to `libsecret`. The plugin
looks it up on each poll with:

```sh
secret-tool lookup service dms-jira account you@example.com
```

so you can rotate the token without restarting DMS.

## Security notes

- The API token is stored in a plain file you control (recommended mode
  0600). Future versions will support libsecret.
- Cached ticket summaries are persisted to
  `~/.local/state/DankMaterialShell/dmsJira_state.json` unencrypted, so
  the plugin can paint the bar on shell start before the first poll
  completes. If your tickets are sensitive at the filesystem-trust
  boundary, disable polling or clear that file on logout.
- The plugin refuses to send credentials to non-`https://` site URLs and
  refuses to open non-`https://` URLs in the browser.

## License

MIT
