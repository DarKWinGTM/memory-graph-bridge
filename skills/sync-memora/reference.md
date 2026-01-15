# Sync-Memora Reference Documentation

## MCP Tool Reference

### Claude-Mem MCP Tools

| Tool | Description | Parameters |
|------|-------------|------------|
| `mcp__plugin_claude-mem_mem-search__help` | Get help documentation | None |
| `mcp__plugin_claude-mem_mem-search__search` | Search observations | `query`, `kind`, `limit` |
| `mcp__plugin_claude-mem_mem-search__get_observation` | Get single observation | `id` |
| `mcp__plugin_claude-mem_mem-search__get_observations` | Get multiple observations | `ids[]` |
| `mcp__plugin_claude-mem_mem-search__get_recent_context` | Get recent observations | `limit` |
| `mcp__plugin_claude-mem_mem-search__timeline` | Get timeline context | `start`, `end` |

### Memora MCP Tools

| Tool | Description | Parameters |
|------|-------------|------------|
| `mcp__memora__memory_list` | List memories | `limit`, `offset`, `category` |
| `mcp__memora__memory_search` | Search memories | `query`, `category`, `tags` |
| `mcp__memora__memory_create` | Create memory | `content`, `category`, `tags`, `section`, `subsection` |
| `mcp__memora__memory_link` | Link memories | `source_id`, `target_id`, `relation`, `bidirectional` |
| `mcp__memora__memory_clusters` | Get memory clusters | `limit` |

---

## Type Mapping Reference

### Claude-Mem → Memora Category

| Claude-Mem `kind` | Memora `category` | Default Tags |
|-------------------|-------------------|--------------|
| `bugfix` | `issue` | `["bugfix", "fix"]` |
| `feature` | `general` | `["feature", "new"]` |
| `discovery` | `general` | `["discovery", "learn"]` |
| `decision` | `general` | `["decision", "important"]` |
| `refactor` | `general` | `["refactor", "improve"]` |
| `change` | `general` | `["change"]` |

### Relationship Types

| Relation | Description | Use Case |
|----------|-------------|----------|
| `references` | Points to related content | Same file mentioned |
| `follows` | Sequential relationship | Same session, sequential |
| `causes` | Causal relationship | Bug → Fix |
| `implements` | Implementation of concept | Decision → Implementation |
| `relates_to` | General relationship | Similar topic |
| `extends` | Builds upon | Enhancement of existing |
| `contradicts` | Conflicting information | Superseded decisions |

---

## Parameter Reference

### --since Options

| Value | Description | Typical Limit |
|-------|-------------|---------------|
| `last_session` | Most recent session | 50 |
| `today` | Current day | 100 |
| `1h` | Last hour | 20 |
| `24h` | Last 24 hours | 100 |
| `7d` | Last 7 days | 500 |

### --types Options

| Value | Description |
|-------|-------------|
| `all` | All observation types |
| `bugfix` | Bug fixes only |
| `decision` | Decisions only |
| `discovery` | Discoveries only |
| `feature` | Features only |
| `refactor` | Refactors only |

---

## MCP Configuration

### Claude-Mem MCP Setup

```json
{
  "mcpServers": {
    "plugin_claude-mem_mem-search": {
      "command": "npx",
      "args": ["-y", "claude-mem"],
      "env": {
        "CLAUDE_MEM_DB": "~/.claude-mem/claude_mem.db"
      }
    }
  }
}
```

### Memora MCP Setup

```json
{
  "mcpServers": {
    "memora": {
      "command": "npx",
      "args": ["-y", "memora-mcp"],
      "env": {
        "MEMORA_DB": "~/.local/share/memora/memories.db"
      }
    }
  }
}
```

---

## Troubleshooting

### MCP Connection Issues

**Claude-Mem not available:**
1. Check if MCP server is running: `ps aux | grep claude-mem`
2. Verify configuration in `~/.claude/settings.json`
3. Check database exists: `ls ~/.claude-mem/`
4. Restart Claude Code

**Memora not available:**
1. Check if MCP server is running: `ps aux | grep memora`
2. Verify configuration in `~/.claude/settings.json`
3. Check database exists: `ls ~/.local/share/memora/`
4. Restart Claude Code

### Sync Issues

**No observations found:**
- Increase time range with `--since 7d`
- Check if claude-mem is recording observations
- Verify database is not empty

**Duplicate detection failing:**
- Check Memora search functionality
- Verify tags are being set correctly
- Review existing memories for conflicts

**Link creation failing:**
- Verify both memory IDs exist
- Check relation type is valid
- Ensure source and target are different

---

## Database Locations

| System | Default Path |
|--------|--------------|
| Claude-Mem | `~/.claude-mem/claude_mem.db` |
| Memora | `~/.local/share/memora/memories.db` |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-01-13 | Initial release |
