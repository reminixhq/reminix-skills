---
name: reminix
description: Use when working with Reminix (reminix.com): reading or changing a workspace in Reminix, from a chat app such as Claude or ChatGPT through its MCP tools, from a terminal through the `reminix` CLI or through its API. Covers signing in, choosing the workspace, every operation, approvals and errors.
---

# Reminix

Reminix turns your scripts into internal tools. Upload a Python, shell or Node script, and Reminix runs it from a form, the API, the CLI or your AI agents, on a schedule or from a webhook. Keys stay out of the code, and every run is on record.

Everything belongs to a **workspace**. You act as the person who signed
you in, with their role in that workspace and only the access they
granted. If they could not do something in the app, neither can you.

## Where you are

- **With Reminix's tools** (Claude, ChatGPT, or an agent that runs on its own; tool names like `workspace_get`): read [tools.md](tools.md) (online: https://reminix.com/docs/skill/tools.md). Use only the tools there.
- **In a terminal** (Claude Code, Codex or another coding agent): read [cli.md](cli.md) (online: https://reminix.com/docs/skill/cli.md). If your client also has the MCP tools, prefer them.
- **In code you are writing**: the HTTP API `https://api.reminix.com/v1` with `Authorization: Bearer <token>`; the OpenAPI document is `https://api.reminix.com/v1/openapi.json`.

## Rules

- Find out which workspace the person means, and ask when it is unclear. Never guess one.
- Read before you change anything, and say what you will change before you do.
- An operation that cannot be undone (a delete) needs the person's explicit go-ahead.
- When nobody is there to answer (you run on a schedule, in the background, or while the person is away): never wait at a sign-in or for an approval. File the approval request, carry on with what does not depend on it, and report what is waiting and why. Never treat silence as a yes.
- Lists return one page: pass `nextCursor` as `cursor` for the next.
- To retry a write after an error or timeout, send the same idempotency key both times so it happens once. Without one, a retried write can happen twice.
- Errors carry `code` and `message`: `unauthorized` → sign in again; `forbidden` → this sign-in lacks the access or role (tell the person what is missing, never work around it); `rate_limited` → wait for `Retry-After`; `invalid_request` → fix the input from `details`.

## For people only

Never do these yourself, even if you could. Ask for approval (below) or tell the person to do it in the app, and why:

- Add an environment: Environments hold secret values and rules. A person sets them up, and an agent asks a person to.
- Delete an environment: Environments hold secret values and rules. A person sets them up, and an agent asks a person to.
- Change an environment: Environments hold secret values and rules. A person sets them up, and an agent asks a person to.
- Download a file: Returns raw bytes; agents use the file's downloadUrl (files.get) instead.
- Upload a file: Takes raw bytes, which an agent's tool call can't carry; agents pass an https link as the file input instead.
- Schedule a script: Schedules and triggers run as the person who created them. Only a person can create or change them.
- Delete a schedule: Schedules and triggers run as the person who created them. Only a person can create or change them.
- Change a schedule: Schedules and triggers run as the person who created them. Only a person can create or change them.
- Archive a script: Stopping a script the team relies on is a person's decision. An agent asks a person to.
- Restore an archived script: Stopping a script the team relies on is a person's decision. An agent asks a person to.
- Change the workspace's script settings: Governance settings are a person's to change; an agent must not loosen them
- Delete a secret: Secret values are a person's to handle; an agent asks the person to add the secret
- Save a secret: Secret values are a person's to handle; an agent asks the person to add the secret
- Remove a secret's value from an environment: Secret values are a person's to handle; an agent asks the person to add the secret
- Run a script when something happens: Schedules and triggers run as the person who created them. Only a person can create one. An agent can ask for a webhook trigger: POST /v1/workspace/approval-requests with action trigger.webhook.create, which a workspace owner approves.
- Delete a trigger: Schedules and triggers run as the person who created them. Only a person can create or change them.
- Rotate a webhook trigger's secret: Only a person can rotate a webhook trigger's secret. An agent can ask: POST /v1/workspace/approval-requests with action trigger.webhook.rotateSecret, which a workspace owner approves.
- Change a trigger: Schedules and triggers run as the person who created them. Only a person can create or change them.
- Redeem an approved request: Returns a secret. Secrets never enter an agent's conversation. The CLI writes them to a file instead.
- Add a webhook endpoint: Changes where workspace data is sent. A person configures webhook endpoints (Settings → Developers in the app).
- Remove a webhook endpoint: Changes where workspace data is sent. A person configures webhook endpoints (Settings → Developers in the app).
- Re-enable a webhook endpoint: Changes where workspace data is sent. A person configures webhook endpoints (Settings → Developers in the app).
- Update a webhook endpoint: Changes where workspace data is sent. A person configures webhook endpoints (Settings → Developers in the app).

## When a person must approve

Some actions are never yours to take alone: creating an API key, adding or removing a webhook endpoint, and others Reminix lists. ASK instead, and wait for a person. You file a request with your reason and show the person its approval link; they decide in Reminix. A secret it creates goes to the person, never to you, and you never ask them to paste one. Requests expire after 24 hours; a denied request is final, so ask the person what they want instead. How: tools.md or cli.md.

## More

- Documentation for agents: https://reminix.com/docs/llms.txt (index) and https://reminix.com/docs/llms-full.txt (everything)
- Errors: https://reminix.com/docs/errors/

## What Reminix is

Reminix makes a team's **scripts** (including AI-written code) shared,
governed units the team runs from a UI, the API, an agent, or a schedule.
Each published change is a new version; runs that need a person go
through approvals; secrets are used by runs and never shown back.

## Who you're helping

Two kinds of people use Reminix through an agent:

- **People who run scripts** (support, operations, finance), usually in
  a chat app such as Claude or ChatGPT: "Refund $40 on order
  A-10423." They don't see code. Every step for them works with
  the MCP tools alone; the CLI does the same from a terminal.
- **People who publish scripts** (developers), usually in a coding
  agent with a terminal such as Claude Code or Codex: "Publish
  scripts/refund.py." Publishing a
  folder needs the CLI (`reminix upload` packs it).

Ask which one you're helping when the request doesn't make it clear.

## Find the right script

1. Search by what the person wants done: `scripts_list` with `q`
   (words) or `tag` (`reminix scripts list --q "refund"`). Read the
   name, description and owner of each match.
2. Open the likely one (`scripts_get`): its README, its inputs (the
   input schema: titles, which are required) and its published version.
3. Several fit: show them and let the person pick. None fits: say so,
   and suggest asking a developer on the team to publish one. Never
   run a script you only guessed at.

## Run a script

1. **Inputs:** take each value from the person's words. A required
   input they didn't give (an order number, an amount, a month): ask.
   Never make one up or reuse one from an earlier run.
2. **Say what will happen** before a script that changes something
   (a refund, an email, a sync): which script, with which inputs, in
   which environment. Wait for a yes. A script that only reads can run
   at once.
3. **Run it:** the script's own tool (named after it:
   `refund-customer` → `refund_customer`), or `runs_create` with
   `{ "inputs": {…} }` (`reminix run <slug> --input '<json>'`). A 400
   lists each wrong field in `details`: fix it from there.
4. **Read the answer:**
   - finished: tell the person the result from `output` in plain
     words;
   - **202, awaiting approval** (the script's rule): see "When a run
     waits for approval" below;
   - **202, running** (a long script): read `runs_get` and `runs_log`
     until it ends; the log's `::progress` lines say how far it is;
   - failed: see "When a run fails".
5. **Files:** a file input takes an `https` link to the file (Reminix
   downloads it); from a terminal, `--file name=./path`. Files the run
   saved are on the run (`runs_get` → `files[]`), each with a one-hour
   `downloadUrl` to hand to the person.

## When a run waits for approval

The approval is a person's decision, made in Reminix, never yours: you
cannot approve a run, and must not say it is approved until Reminix
says so.

1. Keep the run's `id` from the answer.
2. Tell the person in one line what waits and give the link as a
   clickable link: "This refund needs an approval. If you can approve
   it, open [Approve the refund](<the approveUrl>); otherwise your
   workspace's owners and admins have been asked." Say they can come
   back here once it's approved.
3. When they come back (or ask how it went), read the run (`runs_get`)
   and say where it stands, without asking them for anything again:
   - still `awaiting_approval`: nobody has decided yet. Give the link
     again;
   - running or finished: carry on as in "Run a script" step 4 and give
     the result;
   - `cancelled`: its `error` says why (denied, or the request expired).
     Tell them, and don't run it again unless they ask.

## Check what happened

- Recent runs of a script: `runs_list` with `script` (`reminix runs
list --script <slug>`), newest first. Each shows who started it, how
  (`via`: app, api, cli, agent, schedule or trigger), its status and when.
- One run: `runs_get` for its inputs, output, error and files;
  `runs_log` for what it printed while it ran.
- Who changed what in the workspace: `workspace_audit_events_list`.

## When a run fails

1. Read the run's `error` and the end of its log (`runs_log`), and tell
   the person what went wrong in plain words.
2. A wrong or missing input: fix it with the person and run again.
3. Anything else (a secret with no value, an API that refused, a bug):
   don't retry a script that changes things. A failed refund may have
   half happened. Tell the person, name the script's owner
   (`scripts_get`), and let them decide.
4. `403` "Agents cannot run scripts in <env>": the team keeps agents
   out of that environment. Say so; don't try another one.

## Publish a script

**Which script:** when the person asks you to publish one of their
scripts without naming it, list the scripts in their project that people
run by hand, one line each on what it does, and wait for them to pick
one. Change nothing until they do.

**Search first:** `scripts list --q "<words>"` (or `--tag`) — if the
team already has one that does the job, use it (and tell the person)
instead of writing a new one.

A script is a **command** the team already runs (`python report.py
--month 2026-09`, a shell script, a Node or TypeScript CLI), run in its
own fresh machine. Never rewrite it into another language or wrap it
in a framework: describe how it runs.

To publish a script from the person's repository:

0. **Upload the folder as it is:** `reminix upload <folder> --dry-run`
   shows what Reminix found (pyproject.toml, package.json, or its one
   script) and the command it would run, uploading nothing; then
   `reminix upload <folder>`, with `--command` for inputs
   (`{name}` where each goes). That's the default. Customize with a
   `reminix.json`, as below, when the script takes inputs people fill in
   (a form needs labels), calls APIs (hosts, secrets), or when publish
   can't work out how it runs.
1. **Customize it:** in the script's folder,
   `reminix init "python report.py [--month {month}]"`
   (or `reminix init report.py`, or no argument when the folder has one
   script file; `--dir <folder>` for another folder) writes
   `reminix.json` with that `command`, a string input per `{name}`
   (required unless inside `[ … ]`), and a `README.md`. It never writes
   code.
2. **Inputs:** find what the script takes (arguments, environment
   variables, prompts, constants someone would change) and describe
   them in `inputSchema` (`"type": "object"`, each property with a
   `title`, `description` and the right type: that is the form people
   fill in and the MCP tool's parameters). Fill `name`, `description`
   and `tags`, and write the README: what it does, when to use it, what
   the inputs mean.
3. **The command** (`"command"` in `reminix.json`) is never run through
   a shell. Words split on spaces; `'…'` and `"…"` keep spaces in one
   word; `$`, `|`, `>`, `*` are ordinary characters (for a pipeline,
   write `sh -c '…'` or use a `.sh` file). The first word is the program
   (`python`, `node`, `sh`, `./tool`).
   - Never put `{name}` inside inline code (`sh -c`, `python -c`,
     `node -e`, `perl -e`, `ruby -e`, `php -r`): publishing refuses it,
     since the interpreter would run the value as code. Pass it as its
     own argument: `sh -c 'echo "$1"' _ {name}`,
     `python -c 'import sys; print(sys.argv[1])' {name}`.
   - `{name}` is an input. A word that is only `{name}` becomes one
     argument (one per item for a list, none for a true/false input);
     inside a word (`--month={month}`) the value is put in place.
   - `[ … ]` is an optional part, left out when any input in it is
     empty (missing, null, `""`, false, an empty list); not nested; at
     least one input in it. `[--dry-run {dryRun}]` with a boolean
     `dryRun` adds `--dry-run` only when true.
   - An input outside `[ … ]` must be in `inputSchema.required`.
4. **Inputs reach the code three ways at once:** in the command; as
   environment variables `REMINIX_INPUT_<NAME>` (`orderId` →
   `REMINIX_INPUT_ORDER_ID`; strings as they are, other values as JSON);
   and all of them as one JSON object on standard input and in the file
   named by `$REMINIX_INPUTS_FILE`. Use the JSON for nested objects and lists.
   `$REMINIX_ENVIRONMENT` is the run's environment name.
5. **The result:** standard output, kept as JSON when it parses as JSON,
   as text otherwise (up to 1 MB). Exit code 0 means succeeded; any
   other code fails the run, with the last lines of standard error as
   its error. Standard error is the run's log, streamed while it runs.
   So print the result (one JSON object is best) to stdout and
   everything else to stderr.
6. **Dependencies:** a `requirements.txt` (pip), else a `pyproject.toml`
   (`pip install .`), and a `package.json` (`npm ci` with a lock file,
   else `npm install`; no dev dependencies) are installed once, when the version is uploaded, with only the
   package registries reachable. `"install"` in `reminix.json` replaces
   that with your own command (run with `sh -c` in the folder). A failed
   install fails the upload with its output. Runs never install
   anything. The image has Python 3.12, Node 22 (runs `.ts` files
   directly), `sh`, `curl`, `jq` and `pdftotext`; each run gets 1/4
   vCPU, 1 GB memory and 4 GB disk, and nothing carries over between
   runs.
7. **Secrets and network:** never put a key in code or `reminix.json`.
   Ask the person to save it as a Reminix secret (`secrets set`), attach
   it in `reminix.json` (`secrets`, or `envVars` to read it as an environment variable), and list
   in `hosts` only the hosts it calls; nothing else is reachable.
8. **Upload and test:** `reminix upload <folder>` packs the
   folder as it is, its source (git-tracked and untracked-not-ignored files; never
   `node_modules`, `.venv`, `venv`, `__pycache__`, `.deps`, `.git`,
   `.env`; at most 10 MB packed) and uploads a DRAFT version. Always
   `upload`: `reminix publish` also publishes — makes the version the
   one runs use. Then `reminix run <slug> --number <version> --input '<json>' --log`
   (`--number`, since `--version` prints the CLI's version)
   test-runs it with realistic inputs (a test run: it counts toward the
   month's runs, but starts no triggers). Fix and re-upload until it
   works. Several folders, or a parent folder (each subfolder that is a
   script: a `reminix.json`, or a layout publish recognises), upload in
   one command: `reminix upload scripts/`.
9. **Ask to publish:** show the person what it does and the test run's
   output, and **ask them to publish** that version (in the app, or let
   them run `reminix publish <folder> --number <version>`, which publishes the
   tested version without uploading again). Never publish on your own; if
   publishing needs a second person, give them the approval link. Every
   change is a new version.

Over the API, "Upload a version" (`scriptVersions.upload`,
`POST /v1/scripts/{slug}/versions`) takes `{ source,
command?, install?, inputSchema?, outputSchema?, hosts?, secrets?, env?,
timeoutSeconds?, readme?, tags?, create?, name?, description?, publish? }`,
where `source` is the folder as a base64 `.tar.gz`. Without `command`,
Reminix works it out from the folder as `publish` does; without
`inputSchema`, it is made from the command's `{name}`s; without
`readme`, it is the folder's `README.md`. `create: true` makes a new
script; `publish: true` publishes in the same call (or answers with an
`approval` link). As an agent, upload drafts only. Prefer the CLI, which
packs the folder.

A script that takes longer than 30 seconds needs `"timeoutSeconds"`
(up to 900) in `reminix.json`; its runs answer **202 —
running** at once: read the run (`runs.get`) and its log (`runs.log`)
until it ends. The script moves the progress bar by writing a line
`::progress 40 Synced 400 of 1,000 rows` (a percentage, then a
message) to standard error,
and a person (or you, if you started it) can `runs.cancel` it.

A run may answer **202 — awaiting approval** (the script's policy):
follow "When a run waits for approval". A script you cannot see is "not found" — don't guess others.

## Change a published script

Every change is a new version; the published one keeps running until
the person publishes another.

1. Get the script's folder from the person's repository (`scripts_get`
   names the latest version; `script-versions list <slug>` lists them).
2. Change it, `reminix upload <folder>` (a draft), and test-run the
   draft with realistic inputs (`reminix run <slug> --number <version>
--input '<json>' --log`).
3. Show the person what changed and the test run's output, and ask
   them to publish it. Never publish on your own.

A run's code reaches only its version's `hosts` (none by default) and
has at most 30 seconds, unless the version sets a longer
`timeoutSeconds`.

## Files

- Declare a file input with `"format": "file"` (optionally
  `"contentMediaType": "text/csv"`); several files: `{"type": "array",
"items": {"type": "string", "format": "file"}}`.
- In the script, a file input is a path: `{adjustments}` in the command
  becomes `/inputs/adjustments/<file name>`; `$REMINIX_INPUT_ADJUSTMENTS` is the
  path; the file at `$REMINIX_INPUTS_FILE` (and stdin) gives `{ path, name, mediaType, size }`. A
  list becomes several arguments, and a JSON list of paths in the
  environment.
- Save result files by writing them to `$REMINIX_SAVED` (`/saved`, subfolders
  kept in the name), as a monthly report saves `revenue-2026-09.csv`.
  They are the run's **saved files** (never call them outputs: a run's `output` is its stdout JSON), kept whether the run succeeds or fails; files over
  the plan's size or count are left out, with a log line. Keep stdout
  for the small JSON result (1 MB max).
- Running a script with a file input: as an agent (MCP), pass an
  `https` link as the input's value; Reminix downloads it (public hosts
  only, the plan's size). Through the API: `POST /v1/files?name=adjustments.csv`
  with the raw bytes (a `Content-Type`, or none: the media type comes from the
  name) → `{ id: "file_…" }`, then pass
  the id. An upload serves one run and expires after a day unused;
  the id of a file another run saved may be passed as an input.
- From a terminal: `reminix run <slug> --file adjustments=./adjustments.csv
--out ./results` uploads, runs, and downloads what it saved;
  `reminix files upload <path>` / `reminix files download <id>`.
- A script that needs a file for every run can't be scheduled.
- A run's files are on `GET /v1/runs/{id}` → `files[]` (`origin`
  `input`/`saved`), each with a one-hour `downloadUrl` (no key needed) —
  hand that link to the person. `files.get` refreshes the link.

## Secrets

A script that needs an API key uses a workspace **secret**. Never
ask the person for a secret's value, never put one in code, `reminix.json`,
inputs or a command line. List what exists with `secrets list` (names,
and each environment's hosts — never values); if the one you need is
missing, tell the person its name, hosts and environment and ask them
to add it in Settings → Scripts (or with
`reminix secrets set NAME --hosts … --environment <env>`, the value
piped in).

To call an API from a script, declare in `reminix.json`:
`"hosts": ["api.example.com"]` and, per secret,
`"secrets": { "NAME": { "hosts": ["api.example.com"], "header": "Authorization", "format": "Bearer {secret}" } }`
(`hosts` may list a sandbox and a live host; `host` takes one).
The code calls the API WITHOUT credentials (Python's `urllib` or
`requests`, `curl`, Node's `fetch` all work unchanged): Reminix attaches
them on the way out and the code doesn't hold them. A request to any
other host gets an HTTP 403 at once, whose body says why. Use `"envVars":
["NAME"]` (the value as the environment variable `NAME`) only when the
person says the secret allows it (`allowEnvVar`).

## Environments

A workspace has one or more **environments** (`environments list`):
`production` by default, often `staging`. Each has its own value for
every secret and its own rules (approval; whether agents may act).
With one, ignore them. With two or more:

- A run uses the workspace's default unless you name one: `X-Environment`
  over HTTP, `--environment <name>` on the CLI, the
  `environment` argument on a script's MCP tool (`reminix_environment`
  when the script has an input called `environment`). Every answer
  names the environment it acted in — check it.
- **Try things in staging first** and tell the person before running in
  production.
- A run whose secret has no value in its environment fails with "NAME
  has no value in <env>. Add it in Settings → Scripts.": ask the person
  to add it; never switch environments to get around it.
- `403` "Agents cannot run scripts in <env>" means the person kept
  agents out of that environment: tell them, don't retry elsewhere.
- Script code reads `$REMINIX_ENVIRONMENT` (e.g. to pick a test or live base
  URL); list both hosts in `hosts`.
- Adding, changing or deleting environments is a person's job (in
  Settings → Scripts); you can only list and read them.

## Schedules and triggers

To run a script on a timetable or on an event, tell the person to add
a schedule or trigger on its page (they run as whoever creates them, in
the environment they choose; you cannot create them). For example, a
`monthly-report` script whose `month` input defaults to last month runs
at 8:00 on the 1st (`0 8 1 * *`) with no inputs, and an
`alert-on-failure` script runs on `run.failed` filtered to
`{"script.slug": "nightly-sync"}`, with inputs
`{"runId": "{{event.data.runId}}", "error": "{{event.data.error}}"}`. You can list them: `schedules list`,
`triggers list`. Runs they start show `via: "schedule"` or
`"trigger"`; an agent's runs show `via: "agent"`.
