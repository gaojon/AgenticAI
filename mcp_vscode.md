# MCP Configuration Notes

This document records the `spec-search` MCP configuration applied for three VS Code agent extensions:

- Cline
- GitHub Copilot
- Codex

## Overview

The MCP server being configured is:

- **Server name:** `spec-search`
- **Endpoint:** `http://10.161.176.145:8081/mcp`
- **Auth:** Bearer token in the `Authorization` header

> Note: the original Cline configuration had a leading space before the URL. That was corrected when applying the same configuration to Codex and GitHub Copilot.

---

## 1. Cline

### Config file

`/home/jongao/.cline/data/settings/cline_mcp_settings.json`

### Current configuration

```json
{
  "mcpServers": {
    "spec-search": {
      "autoApprove": [
        "search_specs",
        "get_section",
        "get_page",
        "list_docs",
        "get_toc"
      ],
      "disabled": false,
      "timeout": 60,
      "type": "streamableHttp",
      "url": " http://10.161.176.145:8081/mcp",
      "headers": {
        "Authorization": "Bearer rdyLzaYHOAyKcAbm-RCf6179vcdb_SUsT7CW78SKdeQ"
      }
    }
  }
}
```

### Notes

- Cline uses `streamableHttp`.
- Cline includes:
  - `autoApprove`
  - `disabled`
  - `timeout`
- The URL currently contains a leading space:
  - `"url": " http://10.161.176.145:8081/mcp"`
- That leading space should be removed if you want to normalize the Cline config too.

Suggested normalized URL:
```json
"url": "http://10.161.176.145:8081/mcp"
```

---

## 2. Codex

### Config file

`/home/jongao/.codex/config.toml`

### Applied configuration

```toml
[projects."/home/jongao/ppt"]
trust_level = "trusted"

[projects."/home/jongao/mcp"]
trust_level = "trusted"

[mcp_servers.spec-search]
url = "http://10.161.176.145:8081/mcp"
tool_timeout_sec = 60
http_headers = { Authorization = "Bearer rdyLzaYHOAyKcAbm-RCf6179vcdb_SUsT7CW78SKdeQ" }
enabled_tools = ["search_specs", "get_section", "get_page", "list_docs", "get_toc"]
default_tools_approval_mode = "approve"
```

### Mapping from Cline to Codex

- `type: "streamableHttp"` → Codex uses `url` for MCP HTTP servers
- `timeout: 60` → `tool_timeout_sec = 60`
- `headers.Authorization` → `http_headers.Authorization`
- `autoApprove` tool list → `enabled_tools`
- Auto-approval behavior approximated with:
  - `default_tools_approval_mode = "approve"`

### Notes

- The URL was normalized to remove the leading space.
- Existing trusted project entries were preserved.

---

## 3. GitHub Copilot in VS Code

### Config file

Workspace-level config:

`/home/jongao/mcp/.vscode/mcp.json`

### Applied configuration

```json
{
  "servers": {
    "spec-search": {
      "type": "http",
      "url": "http://10.161.176.145:8081/mcp",
      "headers": {
        "Authorization": "Bearer rdyLzaYHOAyKcAbm-RCf6179vcdb_SUsT7CW78SKdeQ"
      }
    }
  }
}
```

### Notes

- No detectable VS Code user-profile MCP config directory was present on this machine at the time of setup.
- Because of that, the configuration was applied at the **workspace level** using `.vscode/mcp.json`.
- VS Code / GitHub Copilot should load this MCP server for this workspace.
- VS Code may ask you to trust the server on first start.

---

## Summary Table

| Extension | Config Location | Transport Type | Auth Header | Notes |
|---|---|---|---|---|
| Cline | `/home/jongao/.cline/data/settings/cline_mcp_settings.json` | `streamableHttp` | Yes | Current file still has leading space in URL |
| Codex | `/home/jongao/.codex/config.toml` | HTTP MCP via `url` | Yes | Applied successfully |
| GitHub Copilot | `/home/jongao/mcp/.vscode/mcp.json` | `http` | Yes | Applied at workspace level |

---

## Recommended cleanup

If you want all three configs to be fully consistent, update the Cline URL from:

```json
"url": " http://10.161.176.145:8081/mcp"
```

to:

```json
"url": "http://10.161.176.145:8081/mcp"
```

This avoids malformed URL / streaming issues caused by the leading space.
