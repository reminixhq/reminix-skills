# Reminix through its tools: the MCP server

For an agent with Reminix's MCP tools: Claude, ChatGPT, an always-on agent or another client connected to `https://mcp.reminix.com/mcp`. Connecting opened the person's browser to sign in, pick ONE workspace and the access, so the tools you see are exactly what they allowed. Read SKILL.md first.

- A tool that changes something takes an `idempotencyKey`: pass the same one when you retry it. The tools already retry a failed attempt with the same key.
- When a tool is missing, the person did not grant that access. Tell them what it would take; do not look for another way.
- **Approvals:** `workspace_approval_actions_list` shows what can be asked and its input; `workspace_approval_requests_create` files it with your reason. Show the person the returned `approveUrl`; `approval_requests_get` tells you when it is decided.

## Tools

| Tool | What it does |
| --- | --- |
| `environments_get` | Get an environment |
| `environments_list` | List environments |
| `files_get` | Get a file |
| `files_list` | List files |
| `runs_cancel` | Cancel a run |
| `runs_create` | Run a script |
| `runs_get` | Get a run |
| `runs_list` | List runs |
| `runs_log` | Get a run's log |
| `schedules_list` | List a script's schedules |
| `scripts_create` | Create a script |
| `scripts_get` | Get a script |
| `scripts_list` | List scripts |
| `scripts_update` | Change a script |
| `script_settings_get` | Get the workspace's script settings |
| `script_versions_list` | List a script's versions |
| `script_versions_publish` | Publish a version |
| `script_versions_test_run` | Test-run a version |
| `script_versions_upload` | Upload a version |
| `secrets_list` | List secrets |
| `triggers_list` | List a script's triggers |
| `workspace_approval_actions_list` | List the actions you can ask approval for |
| `workspace_approval_requests_create` | Ask a person to approve an action |
| `workspace_approval_requests_get` | Check an approval request |
| `workspace_audit_events_list` | List workspace audit events |
| `workspace_event_types_list` | List event types |
| `workspace_get` | Get the current workspace |
| `workspace_invitations_list` | List pending invitations |
| `workspace_members_list` | List workspace members |
| `workspace_teams_list` | List teams |
| `workspace_usage` | Get your plan's usage this period |
| `workspace_webhook_deliveries_list` | List an endpoint's deliveries |
| `workspace_webhook_deliveries_redeliver` | Redeliver an event |
| `workspace_webhook_endpoints_get` | Get a webhook endpoint |
| `workspace_webhook_endpoints_list` | List webhook endpoints |
| `workspace_webhook_endpoints_test` | Send a test event |
