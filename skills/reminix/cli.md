# Reminix from a terminal: the `reminix` CLI

For a coding agent with a shell (Claude Code, Codex or another coding agent). Read SKILL.md first.

- **Install and sign in:** `npm install -g @reminix/cli`, then `reminix login` (the person approves it in the browser). Without a browser, the person sets `REMINIX_TOKEN` to a personal access token.
- Add `--json` to EVERY command: output is the API's JSON, errors are JSON on stderr (with `details` per field and a `docs` link). Exit code 0 is success, 1 a failure, 2 a wrong command or flag (read its `--help`), 4 not signed in (ask the person to run login).
- A list field takes its flag once per value (`--event-type a --event-type b`); `--no-<flag>` turns a setting off; `--clear-<field>` empties one that can be empty.
- Find the workspace first (`reminix workspace list --json`); pass `--workspace <slug>`.
- Pass `--yes` to a command that cannot be undone only after the person's explicit go-ahead.
- A list prints one page; `--all` prints every page, or `--cursor <nextCursor>` the next. Retrying a write: `--idempotency-key <key>`, the same both times; the CLI already retries a failed attempt with the same key.
- **Approvals:** `reminix workspace approval-actions list --json`; `reminix workspace approval-requests create --action <id> --input '{…}' --reason "…" --json` (show the person the `approveUrl`). For an action that creates a secret, `reminix workspace approval-requests redeem <id> --wait --write-env .env` waits for the decision and writes the secret into the file WITHOUT printing it.

## Commands

| Command | MCP tool | What it does |
| --- | --- | --- |
| `reminix environments get <environment>` | `environments_get` | Get an environment |
| `reminix environments list` | `environments_list` | List environments |
| `reminix files get <fileId>` | `files_get` | Get a file |
| `reminix files list` | `files_list` | List files |
| `reminix runs cancel <runId>` | `runs_cancel` | Cancel a run |
| `reminix run <slug>` | `runs_create` | Run a script |
| `reminix runs get <runId>` | `runs_get` | Get a run |
| `reminix runs list` | `runs_list` | List runs |
| `reminix runs log <runId>` | `runs_log` | Get a run's log |
| `reminix schedules get <slug> <scheduleId>` | `schedules_get` | Get a schedule |
| `reminix schedules list <slug>` | `schedules_list` | List a script's schedules |
| `reminix scripts create` | `scripts_create` | Create a script |
| `reminix scripts get <slug>` | `scripts_get` | Get a script |
| `reminix scripts list` | `scripts_list` | List scripts |
| `reminix scripts update <slug>` | `scripts_update` | Change a script |
| `reminix script-settings get` | `script_settings_get` | Get the workspace's script settings |
| `reminix script-versions get <slug> <version>` | `script_versions_get` | Get a version |
| `reminix script-versions list <slug>` | `script_versions_list` | List a script's versions |
| `reminix script-versions publish <slug> <version>` | `script_versions_publish` | Publish a version |
| `reminix script-versions test-run <slug> <version>` | `script_versions_test_run` | Test-run a version |
| `reminix upload [dir...]` | `script_versions_upload` | Upload a version |
| `reminix secrets get <name>` | `secrets_get` | Get a secret |
| `reminix secrets list` | `secrets_list` | List secrets |
| `reminix trigger-calls list <slug> <triggerId>` | `trigger_calls_list` | List a webhook trigger's calls |
| `reminix triggers get <slug> <triggerId>` | `triggers_get` | Get a trigger |
| `reminix triggers list <slug>` | `triggers_list` | List a script's triggers |
| `reminix user me` | — | Get the current user |
| `reminix user revoke-token` | — | Revoke the token making this request |
| `reminix workspace approval-actions list` | `workspace_approval_actions_list` | List the actions you can ask approval for |
| `reminix workspace approval-requests create` | `workspace_approval_requests_create` | Ask a person to approve an action |
| `reminix workspace approval-requests get <approvalRequestId>` | `workspace_approval_requests_get` | Check an approval request |
| `reminix workspace audit-events list` | `workspace_audit_events_list` | List workspace audit events |
| `reminix workspace event-types list` | `workspace_event_types_list` | List event types |
| `reminix workspace get` | `workspace_get` | Get the current workspace |
| `reminix workspace invitations list` | `workspace_invitations_list` | List pending invitations |
| `reminix workspace members list` | `workspace_members_list` | List workspace members |
| `reminix workspace plan-usage get` | `workspace_plan_usage_get` | Get your plan's usage this period |
| `reminix workspace teams list` | `workspace_teams_list` | List teams |
| `reminix workspace webhook-deliveries list <endpointId>` | `workspace_webhook_deliveries_list` | List an endpoint's deliveries |
| `reminix workspace webhook-deliveries redeliver <endpointId> <deliveryId>` | `workspace_webhook_deliveries_redeliver` | Redeliver an event |
| `reminix workspace webhook-endpoints get <endpointId>` | `workspace_webhook_endpoints_get` | Get a webhook endpoint |
| `reminix workspace webhook-endpoints list` | `workspace_webhook_endpoints_list` | List webhook endpoints |
| `reminix workspace webhook-endpoints test <endpointId>` | `workspace_webhook_endpoints_test` | Send a test event |

`reminix <command> --help` lists each command's inputs; `reminix api <method> <path>` calls any endpoint directly.
