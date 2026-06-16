## CodeGraph — Codebase Intelligence

This project is indexed by CodeGraph. Use CodeGraph MCP tools instead of grep/find/cat for code analysis. The pre-built index provides instant, semantic, relationship-aware results.

### Tier 1 — Start Here

- `codegraph_context` — Describe your task, get all relevant code, relationships, and structure in one call. Use this FIRST before reading files.

### Tier 2 — Drill Down

- `codegraph_callers` — Who calls this function? (replaces grep for function name)
- `codegraph_callees` — What does this function call? (replaces reading function body)
- `codegraph_node` — Get full source code of a specific symbol (replaces cat/read on whole files)
- `codegraph_query` — Search symbols by name or semantic meaning (replaces grep/find)
- `codegraph_dependencies` — Module/file dependency tree (replaces reading imports)
- `codegraph_find_references` — All usages of a symbol across the project (replaces project-wide grep)

### Tier 3 — Specialized

- **Structure:** `codegraph_structure`, `codegraph_impact`, `codegraph_stats`, `codegraph_circular_imports`, `codegraph_project_tree`, `codegraph_export_map`, `codegraph_import_graph`, `codegraph_file`, `codegraph_diagram`, `codegraph_dead_code`, `codegraph_tests`, `codegraph_frameworks`, `codegraph_languages`
- **Git:** `codegraph_blame`, `codegraph_file_history`, `codegraph_recent_changes`, `codegraph_commit_diff`, `codegraph_symbol_history`, `codegraph_branch_info`, `codegraph_modified_files`, `codegraph_hotspots`, `codegraph_contributors`
- **Security:** `codegraph_scan_security`, `codegraph_check_owasp`, `codegraph_check_cwe`, `codegraph_explain_vulnerability`, `codegraph_suggest_fix`, `codegraph_find_injections`, `codegraph_taint_sources`, `codegraph_security_summary`, `codegraph_trace_taint`
- **Data Flow:** `codegraph_find_path`, `codegraph_complexity`, `codegraph_data_flow`, `codegraph_dead_stores`, `codegraph_find_uninitialized`, `codegraph_reaching_defs`

### Working Agreements

- Do NOT use `grep` or `find` to search for symbols — use `codegraph_query` or `codegraph_callers`
- Do NOT read entire files to find a function — use `codegraph_node("functionName")`
- Do NOT manually trace imports — use `codegraph_dependencies("file.ts")`
- Do NOT use `git log` or `git blame` via shell — use `codegraph_file_history` or `codegraph_blame`

### Project Stats
- Languages: N/A
- Symbols: 0 | Relationships: 0
