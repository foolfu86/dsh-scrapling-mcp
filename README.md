# Scrapling MCP bundle for DeepSeek Harness

Wraps the MCP server that ships with [Scrapling](https://github.com/D4Vinci/Scrapling) so the
agent gets Scrapling's HTTP fetch and stealth-browser scraping tools in every session.

This bundle contains no runtime code of its own — it only inserts the generic MCP client
(`@deepseek-ai/dsh-mcp-client`) pointed at the `scrapling-mcp` console script over stdio.
It has no build step and no npm dependencies of its own.

## Host prerequisites

- Python 3.10+ with `scrapling[ai]` (the extras pull in the MCP server dependencies):

  ```bash
  pip install "scrapling[ai]"
  ```

  This puts the `scrapling-mcp` console script on your `PATH`; `cordis.patch.yml` calls it by
  name, so no absolute path has to be edited.

- A Chromium-compatible browser. Install one with Scrapling's own downloader:

  ```bash
  scrapling install
  ```

`failOnStartupError: false` means the harness starts normally even when `scrapling-mcp` or
Chromium is not available yet; the tools simply become available once the prerequisites are
installed and the entry is reloaded.

## What it adds

| Group | Tools |
| --- | --- |
| HTTP (no JS) | `make_request`, `open_request_session`, `session_make_request` |
| One-shot browser | `browser_fetch_once` |
| Browser sessions | `browser_open`, `browser_fetch`, `browser_extract`, `browser_actions`, `browser_evaluate`, `browser_screenshot`, `browser_observe`, `browser_network_requests`, `browser_network_request`, `browser_save_state`, `browser_load_state` |
| Housekeeping | `list_sessions`, `close_session` |

## Files

- `cordis.patch.yml` — inserts the MCP client entry `scrapling-mcp` (stdio transport).
- `package.json` — bundle metadata (`dsh.bundle.patch`, exported locale + icon).
- `locale/en.json`, `locale/zh.json` — plugin inventory title/description.
- `icon.svg` — inventory icon.

## Install

Add the bundle from GitHub with the harness plugin manager:

```bash
dsh plugin --profile desktop add github:foolfu86/dsh-scrapling-mcp
```

Use whichever profile name you want the tools available in (for example `demo` for a throwaway
test profile). Verify the config without booting the app:

```bash
dsh --profile desktop --dump-config
```

Remove it again with:

```bash
dsh plugin --profile desktop remove dsh-scrapling-mcp
```

You can also install through the agent tool `plugin_manager` (action `install_bundle`) or the
desktop plugin page, pointing at this directory. The MCP client is configured at entry load
time, so after editing `cordis.patch.yml` disable and re-enable the entry (or restart the app).

## Portability

`cordis.patch.yml` contains no host-specific paths: it calls `scrapling-mcp` from `PATH` and
lets Scrapling find its own Chromium installation. Install the prerequisites on the target
machine and the same bundle works unchanged.
