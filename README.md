# nxm-gateway-mcp

**A thin federated MCP gateway (fan-out + RRF) over nxm-memory DB nodes.**

A stateless proxy that exposes **one MCP endpoint** and fans each query out to
configured **nxm-memory DB nodes**, fusing their results with **Reciprocal Rank
Fusion (RRF)**. It holds **no index and loads no model** — the heavy lifting
(indexing, embedding, search) happens in the DB nodes it talks to.

---

## What it does

| Mode | Tools | Behavior |
|------|-------|----------|
| **Federated search (RRF)** | `index_search`, `search_code`, `search_docs`, `search_exact`, `search_regex`, `memory_recall` | Query all nodes in parallel, fuse with RRF, return top hits with `source_node` |
| **Fan-out-first** | `get_chunk` | Probe all nodes, return first that has the item |
| **Passthrough** | `context_compress`, `context_budget`, `find_symbol`, `outline`, `find_references`, `stats`, `memory_remember`, `index_workspace`, `workspace_list`, `workspace_create` | Forward to one node (routed by optional `workspace` arg, else first node) |

## Configuration

Nodes are resolved at startup, in order of precedence:

1. **`NXM_GATEWAY_NODES` env var** — `name:port,name:port,...`
2. **`~/.nxm/memory/config.toml`**, section `[gateway].nodes`

```toml
[gateway]
[[gateway.nodes]]
name = "engine"
port = 7169
enabled = true
```

## Run

```bash
# JSON-RPC over stdio
nxm-gateway-mcp
```

Configure in an MCP-compatible agent:

```json
{
  "mcpServers": {
    "nxm-gateway": {
      "command": "nxm-gateway-mcp",
      "env": { "NXM_GATEWAY_NODES": "engine:7169,vendors:7173" }
    }
  }
}
```

## Documentation

- **English**: [README_ENG.md](README_ENG.md)
- **Italiano**: [README_ITA.md](README_ITA.md)

## License

Dual-licensed under **MIT OR Apache-2.0**.