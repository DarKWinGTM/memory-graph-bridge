---
name: sync-memora
description: |
  Sync Claude-Mem observations to Memora memory graph with AI-powered analysis.
  Use when user wants to sync memories, transfer observations, create memory links,
  or visualize memory relationships between claude-mem and memora systems.
  Triggers: "sync memories", "transfer to memora", "link memories", "memory graph"
allowed-tools:
  - mcp__plugin_claude-mem_mem-search__help
  - mcp__plugin_claude-mem_mem-search__search
  - mcp__plugin_claude-mem_mem-search__get_observation
  - mcp__plugin_claude-mem_mem-search__get_observations
  - mcp__plugin_claude-mem_mem-search__get_recent_context
  - mcp__plugin_claude-mem_mem-search__timeline
  - mcp__memora__memory_list
  - mcp__memora__memory_list_compact
  - mcp__memora__memory_hybrid_search
  - mcp__memora__memory_semantic_search
  - mcp__memora__memory_create
  - mcp__memora__memory_update
  - mcp__memora__memory_link
  - mcp__memora__memory_related
  - mcp__memora__memory_clusters
  - mcp__memora__memory_find_duplicates
user-invocable: true
---

# Sync Claude-Mem to Memora (Bidirectional Analysis)

Sync observations from Claude-Mem to Memora with **bidirectional analysis** - analyzing data from BOTH systems before making sync decisions.

## CRITICAL: Bidirectional Analysis Principle

**DO NOT** use one-way sync. **ALWAYS** fetch and analyze data from both systems first.

```
Bidirectional Flow (MANDATORY)

Step 1: Parallel Fetch
  ├─ Claude-Mem → observations to sync
  └─ Memora → existing memories (for context)
  ↓
Step 2: Unified Analysis
  ├─ Compare both datasets
  ├─ Identify duplicates, updates, new items
  └─ Plan optimal sync strategy
  ↓
Step 3: Context-Aware Sync
  ├─ Create only truly new memories
  ├─ Update existing if needed
  └─ Build relationships with full context
```

---

## Step 0: MCP Readiness Check (REQUIRED)

Before any sync operation, verify both MCP servers are available:

### Test Claude-Mem:
- Call: `mcp__plugin_claude-mem_mem-search__help`
- Expected: Returns help documentation

### Test Memora:
- Call: `mcp__memora__memory_list` with limit=1
- Expected: Returns memory list (even if empty)

### If either check fails:
- Report which MCP is unavailable
- Provide troubleshooting guidance
- Do NOT proceed with sync

### Success output:
```
MCP Status Check
─────────────────────────
✅ Claude-Mem: Connected
✅ Memora: Connected
─────────────────────────
Proceeding with bidirectional analysis...
```

---

## Step 1: PARALLEL FETCH (CRITICAL - DO NOT SKIP)

**Fetch data from BOTH systems simultaneously:**

### 1.1 Fetch from Claude-Mem:
```
Call: mcp__plugin_claude-mem_mem-search__get_recent_context
Params: { limit: based on --since parameter }
```

### 1.2 Fetch from Memora (MANDATORY):
```
Call 1: mcp__memora__memory_list_compact
Params: { tags_any: ["claude-mem", "synced"], limit: 100 }
→ Get previously synced memories

Call 2: mcp__memora__memory_hybrid_search
Params: { query: "<topics from observations>", top_k: 20 }
→ Get related existing memories for context
```

### 1.3 Output Parallel Fetch Results:
```
Parallel Fetch Complete
═════════════════════════════════════
📥 Claude-Mem: N observations fetched
📦 Memora (synced): N previously synced memories
🔍 Memora (related): N related memories found
═════════════════════════════════════
```

---

## Step 2: UNIFIED ANALYSIS (CRITICAL - DO NOT SKIP)

**Analyze BOTH datasets together before any sync decision:**

### 2.1 Build Unified View

Create a mental map of:
- All observations from Claude-Mem
- All previously synced memories in Memora
- All related memories in Memora (even if not from claude-mem)

### 2.2 Classify Each Observation

For each Claude-Mem observation, determine action:

| Action | Condition | What to do |
|--------|-----------|------------|
| `CREATE` | No similar memory exists in Memora | Create new memory |
| `SKIP` | Exact duplicate already synced | Skip entirely |
| `UPDATE` | Similar memory exists, content evolved | Update existing memory |
| `LINK_ONLY` | Related memory exists, add relationship | Create link only |

### 2.3 Identify Relationships

With full Memora context, identify:
- Links between new observations and existing Memora memories
- Links between newly synced memories themselves
- Potential clusters and themes

### 2.4 Output Analysis Summary:
```
Unified Analysis Complete
═════════════════════════════════════
📊 Observations analyzed: N

Sync Plan:
  ✅ CREATE: N (truly new)
  ⏭️  SKIP: N (duplicates)
  🔄 UPDATE: N (evolved content)
  🔗 LINK_ONLY: N (add relationships)

Relationships identified:
  - N links to existing Memora memories
  - N links between new observations
═════════════════════════════════════
```

---

## Step 3: Smart Deduplication

### 3.1 Duplicate Detection Methods

**Method 1: Tag-based (Fast)**
- Check if observation ID exists in Memora tags
- Tag format: `obs-<id>` or `claude-mem-<id>`

**Method 2: Semantic (Accurate)**
```
Call: mcp__memora__memory_hybrid_search
Params: { query: "<observation content>", top_k: 5 }
```
- If score > 0.85 → Likely duplicate
- If score 0.6-0.85 → Related, consider LINK_ONLY
- If score < 0.6 → Truly new

### 3.2 Handle Near-Duplicates

If similar but not identical:
- Compare timestamps
- If Memora is newer → SKIP
- If Claude-Mem is newer → UPDATE existing memory

---

## Step 4: Context-Aware Sync

### 4.1 For CREATE actions:

```json
{
  "tool": "mcp__memora__memory_create",
  "params": {
    "content": "<observation content>",
    "tags": ["claude-mem", "synced", "<type>", "obs-<id>", "<extracted tags>"],
    "metadata": {
      "source": "claude-mem",
      "observation_id": "<id>",
      "session_id": "<session_id>",
      "synced_at": "<timestamp>"
    }
  }
}
```

### 4.2 For UPDATE actions:

```json
{
  "tool": "mcp__memora__memory_update",
  "params": {
    "memory_id": "<existing memory id>",
    "content": "<updated content>",
    "tags": ["<existing tags>", "updated"]
  }
}
```

### 4.3 Sync Rules by Type:

| Type | Action | Reason |
|------|--------|--------|
| `decision` | ✅ SYNC | Important architectural/design choices |
| `bugfix` | ✅ SYNC | Problem-solution pairs valuable |
| `discovery` | ✅ SYNC | New learnings worth preserving |
| `feature` | ✅ SYNC | New capabilities documented |
| `refactor` | ⚠️ MAYBE | Sync if significant |
| `change` | ❌ SKIP | Usually trivial |

---

## Step 5: Intelligent Linking (Context-Aware)

### 5.1 Link to Existing Memora Memories

With full Memora context from Step 1, create links:

```json
{
  "tool": "mcp__memora__memory_link",
  "params": {
    "from_id": "<new memory id>",
    "to_id": "<existing memora memory id>",
    "edge_type": "<relation type>",
    "bidirectional": false
  }
}
```

### 5.2 Relationship Types:

| Condition | Relation |
|-----------|----------|
| Same file/component mentioned | `references` |
| Bug → Fix relationship | `causes` |
| Decision → Implementation | `implements` |
| Extends existing concept | `extends` |
| Similar topic/theme | `relates_to` |
| Supersedes old information | `supersedes` |

### 5.3 Link Between New Memories

Also create links between newly synced memories if related.

---

## Step 6: Report Summary

```
Sync Complete (Bidirectional Analysis)
═══════════════════════════════════════════════════
📊 Source Analysis:
   - Claude-Mem observations: N
   - Memora existing memories: N (context)

📋 Sync Results:
   ✅ Created: N new memories
   🔄 Updated: N existing memories
   ⏭️  Skipped: N (duplicates/trivial)
   🔗 Links created: N relationships
      - To existing Memora: N
      - Between new memories: N

📝 Details:
   - Decisions: N synced
   - Bugfixes: N synced
   - Discoveries: N synced
   - Features: N synced

🆕 New memories:
   1. [ID] <title> (decision)
   2. [ID] <title> (bugfix)
   ...
═══════════════════════════════════════════════════
```

---

## Parameters

| Param | Type | Default | Description |
|-------|------|---------|-------------|
| `--since` | string | `last_session` | Time range: `last_session`, `today`, `1h`, `24h`, `7d` |
| `--types` | string | `all` | Filter: `bugfix`, `decision`, `discovery`, `feature`, `all` |
| `--dry-run` | flag | false | Preview without creating |

---

## Examples

```bash
# Sync last session (default)
/sync-memora

# Sync last 24 hours, only decisions
/sync-memora --since 24h --types decision

# Preview what would be synced (with full analysis)
/sync-memora --dry-run
```

---

## Dry Run Mode

When `--dry-run` is specified:

1. Perform ALL analysis steps (including parallel fetch)
2. Show complete sync plan
3. Do NOT execute any writes
4. Output detailed preview:

```
Dry Run Preview (Bidirectional Analysis)
═══════════════════════════════════════════════════
📥 Fetched: N observations from Claude-Mem
📦 Context: N existing memories from Memora

📋 Sync Plan:
   Would CREATE: N memories
   Would UPDATE: N memories
   Would SKIP: N (duplicates)
   Would LINK: N relationships

📝 Preview of changes:
1. [CREATE] "Use JWT for authentication" (decision)
   Tags: claude-mem, synced, decision, auth, jwt
   Links to: Memory #42 (relates_to)

2. [UPDATE] Memory #38 - "Token handling"
   Reason: New bugfix information

3. [LINK_ONLY] Observation #123
   → Link to Memory #42 (implements)
...

Run without --dry-run to execute sync.
═══════════════════════════════════════════════════
```

---

## Error Handling

### MCP Connection Failed
```
❌ Error: Claude-Mem MCP not available

Troubleshooting:
1. Check if claude-mem MCP server is running
2. Verify configuration in ~/.claude/settings.json
3. Ensure database exists at ~/.claude-mem/
```

### No Observations Found
```
ℹ️ No observations found for the specified time range.

Try:
- Increase time range: /sync-memora --since 7d
- Check if claude-mem is recording observations
```

### Memora Write Failed
```
❌ Error: Failed to create memory in Memora

Details: <error message>

Troubleshooting:
1. Check Memora MCP server status
2. Verify write permissions
3. Check database integrity
```
