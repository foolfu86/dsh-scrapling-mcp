# Scrapling MCP bundle for DeepSeek Harness

Wraps the MCP server that ships with [Scrapling](https://github.com/D4Vinci/Scrapling) so the
agent gets Scrapling's 18 scraping tools (`mcp__scrapling__*`) in every session.

This bundle contains no runtime code of its own — it only inserts the generic MCP client
(`@deepseek-ai/dsh-mcp-client`) pointed at the `scrapling-mcp` console script.

## What it adds

| Group | Tools |
| --- | --- |
| HTTP (no JS) | `make_request`, `open_request_session`, `session_make_request` |
| One-shot browser | `browser_fetch_once` |
| Browser sessions | `browser_open`, `browser_fetch`, `browser_extract`, `browser_actions`, `browser_evaluate`, `browser_screenshot`, `browser_observe`, `browser_network_requests`, `browser_network_request`, `browser_save_state`, `browser_load_state` |
| Housekeeping | `list_sessions`, `close_session` |

## Host prerequisites

- Python 3.10+ with `scrapling[ai]` (the extras pull in the MCP server dependencies):
  `pip install "scrapling[ai]"`
- A Chromium-compatible browser. This machine has no Playwright/Patchright download, so
  `cordis.patch.yml` passes `--executable-path` pointing at the locally installed Chrome:
  `C:/Program Files/Google/Chrome/Application/chrome.exe`. Change that path if Chrome lives
  elsewhere, or run `scrapling install` and drop the argument to use a downloaded Chromium.

## Files

- `cordis.patch.yml` — inserts the MCP client entry `scrapling-mcp` (stdio transport).
- `package.json` — bundle metadata (`dsh.bundle.patch`, exported locale + icon).
- `locale/en.json`, `locale/zh.json` — plugin inventory title/description.
- `icon.svg` — inventory icon.

## Install / update

Install with the harness plugin manager (agent tool `plugin_manager`, action `install_bundle`)
or the desktop plugin page, pointing at this directory:

The profile links this directory (`@local/scrapling-mcp` → this folder), so editing
`cordis.patch.yml` takes effect after the entry is disabled and re-enabled (or after the app
restarts) — the MCP client is only configured at entry load time.

## Portability (read before installing on a different machine)

`cordis.patch.yml` contains two absolute paths from the author's Windows host:

- `command`: the `scrapling-mcp` console script (the Python `Scripts` directory).
- `args[1]`: `--executable-path` for a Chromium-compatible browser.

On another machine, edit both values to match your own Python and browser locations,
or put `scrapling-mcp` on PATH and set `command` to `scrapling-mcp` with no `args`.
Everything else in the bundle is portable.
