---
name: migrate-bot-to-hub
description: Migrate a TJM pharmacy bot to the TJM Hub. Find or create its license, publish current code and refreshed dependencies to main through a PR with at least five title words and no description, configure GitHub mode, retrieve and customize the remote environment, upload resources and external secrets, sync the bundle, and return the license code for the user to run it.
---

# TJM Bot Hub Migration

Complete the six steps in order. Dependency preparation belongs **before the PR**, and the PR must merge **before the Hub sync**. The final deliverable is the verified license code plus a brief bundle result. The user enters the code and runs the bot.

Read [references/rx-works.md](references/rx-works.md) for the demonstrated example, remote file discovery, and exceptional Git history. Its identities, commits, and paths are examples, not defaults. Do not store real license codes or secret values in reusable documentation.

## Establish the migration target

Resolve the following from the request, repository, Hub, and saved remote-machine configuration. Ask only when evidence cannot resolve a required value or yields ambiguous matches.

| Information | Evidence to use |
| --- | --- |
| Pharmacy and intended bot/workflow | User request, existing Hub client/license, bot configuration |
| Local repository and GitHub URL | Exact project path and `git remote -v` |
| Intended latest implementation | Fetched branch commits, changes, and actual workflow |
| Remote computer and Connect profile | Saved device alias/hostname and active bot settings |
| Bot environment and required external assets | Active remote bot folder and code references |
| License identity | Matching pharmacy, bot, record ID, and separate code/key |

Creating or editing this skill alone does not authorize a live migration. During an authorized migration, carry existing authorization forward and do not ask again for routine steps already requested. Preserve unrelated local work. Keep production launch under the user's control unless separately requested.

Use project-specific names with branch prefixes `feat/`, `fix/`, or `chore/`, without assistant branding. Treat walkthrough documents as read-only unless the user's exact Maestro authorization directly precedes the edit request.

Store temporary prescription, QA, and investigation artifacts outside repositories under `/tmp/tjm-qa-artifacts/<pharmacy-slug>/<case-or-document-id>/` or Windows `C:\tmp\tjm-qa-artifacts\<pharmacy-slug>\<case-or-document-id>\`. Protect downloaded originals containing secrets. Preserve processing ledgers, recovery markers, and evidence; never start with an empty replacement directory to bypass duplicate-processing safeguards.

## 1. Find or create the license

1. Open `https://hub.tjmlabs.com` in Chrome. Reuse the correct existing tab and session.
2. Use the global field `Search users, licenses, plans...`. Search the pharmacy's display name, then a distinctive term or alternate name if necessary.
3. Inspect the **Licenses** results, not only the client account. Match pharmacy, bot, workflow, and division when shown. If two licenses plausibly match, resolve that ambiguity before changing either.
4. Reuse a matching license even when it is Regular, Under Development, unassigned, or never synced. Those states do not justify a duplicate.
5. If no matching license exists, use New License. Inspect the current form, select the verified client and bot, and fill required values from evidence. Do not invent billing, expiry, or machine details. Ask for a genuinely missing required value.
6. Confirm the saved client/bot identity and capture both the numeric record ID and the actual **license code/key**. The numeric ID in `/license/edit/<record-id>/` is not the code the user should enter.

Keep the code available securely for External Files selection and final handoff. Avoid printing it repeatedly or including it in PRs, documentation, or repository files.

**Completion evidence:** a specific matching license and its verified code. A matching client account alone is insufficient.

## 2. Prepare dependencies and publish current code to main

### Select the intended implementation

In the exact bot repository, inspect the working state and fetch current remote branches:

```sh
git status --short
git branch --show-current
git remote -v
git fetch --prune origin
git for-each-ref --sort=-committerdate --format='%(refname:short) %(committerdate:iso8601) %(objectname:short) %(subject)' refs/remotes/origin
```

Inspect candidate branches with `git log`, `git diff --stat`, and the relevant source changes. Use commit dates to identify candidates, then verify which implements the intended pharmacy workflow. A newer timestamp or promising branch name alone is not sufficient.

Preserve unrelated edits. Reuse a suitable isolated checkout when necessary, or create a project-specific migration branch from the selected implementation. If that implementation already lives on main, base the dependency update on current origin/main. Do not reset or stash the user's work without need.

Check ancestry before merging. Ordinary related histories should use an ordinary PR. Unrelated history requires deliberate reconciliation and tree verification; see the exceptional Rx Works reference. Do not apply an `ours` merge merely to silence conflicts or force-push main.

### Refresh Kroll before the PR

For a Kroll bot, update the existing `tool.uv.sources` entry in **the bot's** `pyproject.toml`:

```toml
[tool.uv.sources]
kroll-automation = { git = "https://github.com/tjmlabs/kroll_package.git", branch = "dev" }
```

Preserve the other TOML entries. This is the **Kroll dependency's** branch; the **bot repository deploys from main**. Do not switch the bot deployment branch to dev.

Run each command and inspect its result before continuing:

```sh
uv sync
uv sync --upgrade-package kroll-automation
uv lock
```

Inspect `uv.lock` for the `kroll-automation` package, the `?branch=dev` source, and its resolved commit. Include the regenerated lockfile and TOML in the PR. New transitive dependencies can be expected; inspect the diff rather than manually pruning the generated lockfile.

If installation fails because of authentication, resolution, or platform support, address that actual failure. Do not silently substitute `--no-sync`, skip a command, or report success. Local installation can use cached credentials and does not establish that the remote Engine has authentication.

Run applicable existing offline checks and `git diff --check`. Do not launch a pharmacy workflow to test packaging. Confirm required runtime entry points and resource references remain present.

### Create and merge the PR

Stage explicit intended files, including `pyproject.toml` and `uv.lock`. Inspect `git diff --cached --stat` and the staged diff. Confirm no environment, credential, prescription, QA, or unrelated files are included. Do not use a blanket `git add -A` without inspecting its scope.

**Every new PR title must contain at least five words.** Use a natural, specific title; count its whitespace-separated words before submitting. Suitable examples:

- `Prepare Rx Works for Hub` — five words.
- `Update Rx Works Kroll dependency` — five words.
- `Migrate Gander Pharmacy to the Hub` — six words.

**The description must be explicitly empty.** This overrides PR templates and normal description-writing defaults. Example, replacing the example branch and title with the actual migration values:

```sh
gh pr create --base main --head chore/rx-works-hub-migration --title 'Prepare Rx Works for Hub' --body ''
```

Attach each created PR to the task when the attachment tool is available. Inspect the PR's title, body, files, head SHA, mergeability, and checks:

```sh
gh pr view <pr-number> --json title,body,files,headRefOid,mergeable,mergeStateStatus,statusCheckRollup
```

Replace angle-bracket placeholders in commands before executing them. Verify the body is empty, the title has at least five words, the diff is intended, and required checks pass. Merge the verified head using the repository's permitted merge method. For a repository permitting merge commits:

```sh
gh pr merge <pr-number> --merge --match-head-commit <verified-head-sha>
git fetch origin
git rev-parse origin/main
```

Verify the PR actually merged and the intended changes are on origin/main. Record the resulting main SHA for the later Hub comparison. Account for legitimate concurrent main changes instead of assuming the merge SHA is still the branch tip.

**Completion evidence:** dependency commands passed, applicable checks passed, an appropriately titled PR with an empty body merged, and the published main commit is recorded.

## 3. Configure GitHub mode

1. Open the verified license's Edit page.
2. Enable `GitHub-Based License` and wait for its repository controls to appear.
3. Enter the exact bot repository's HTTPS clone URL, such as `https://github.com/tjmlabs/<bot-repository>.git`.
4. Set main if the form exposes a branch control. If it does not, verify main in the completed bundle metadata in step 5. Do not claim a branch field was set when none appeared.
5. Preserve unrelated license settings. Inspect required defaults; the demonstrated form populated a one-year expiry for a license without one. Review and disclose a required default actually applied rather than silently inventing it.
6. Save and verify the same license was updated. The demonstrated UI changed its button label to `CREATE LICENSE` while editing; use the record identity and success message to determine what happened.
7. Reopen or inspect the saved configuration to confirm GitHub mode and the correct repository.

**Completion evidence:** the correct existing or newly created license points to the intended repository in GitHub mode. Saving the form alone does not prove bundle creation.

## 4. Retrieve configuration and upload external files

### Find the active bot and retrieve originals

Read and use the installed `tjm-connect-files` skill for the actual transfer adapter and supported commands. Resolve the saved device by alias/hostname, and select the right profile; TJM Labs Connect devices use `--profile labs`. List uncertain directories before downloading. Serialize transfers when the adapter temporarily switches network profiles.

The adapter transfers individual files, not directory trees. List each relevant resource subfolder and download its required files individually, preserving relative paths locally. Never use `--overwrite` without authorization. Do not replace the transfer workflow with remote commands or screen control.

After resolving the adapter's absolute path from that skill, use these command patterns, replacing the example alias and paths with verified values. Set `task_transfer_adapter` to that verified adapter path before running them:

```sh
python3 "$task_transfer_adapter" devices --profile labs
python3 "$task_transfer_adapter" list --profile labs --device 'Rx Works' --remote-path 'C:\Users\<remote-user>\TJM Bot Project\Prescription-Works-bot'
python3 "$task_transfer_adapter" download --profile labs --device 'Rx Works' --remote-path 'C:\Users\<remote-user>\TJM Bot Project\Prescription-Works-bot\.env' --local-path '/tmp/tjm-qa-artifacts/prescription-works/hub-migration/bot-original.env'
```

Use the default profile instead when the device belongs to TJM Connect rather than TJM Labs Connect. Keep the pharmacy directory specific to the actual migration. Download into an available protected location without replacing an existing original.

When the active bot folder is uncertain, locate the Engine directory and retrieve its settings database. Inspect the database schema read-only, then select only the settings needed to locate the bot: automation/partner name, script path, Python executable path, and optional secrets path. The Engine's own `.env` contains Engine configuration and is not automatically the bot's environment.

Download the active bot's `.env` and referenced external credentials. Keep protected originals outside the repository. Do not display complete environments, database rows, private keys, tokens, or password values.

### Customize the deployment environment

Preserve every unrelated original runtime value. Add or update:

```env
SCRIPT_PATH=bot.py
PYTHON_PATH=.venv\Scripts\python.exe
PARTNER_NAME=<pharmacy display name>
AUTOMATION_NAME=<pharmacy bot display name>
BOT_PMS=kroll
BOT_CUSTOMER=<existing pharmacy logging identifier>
BOT_FLOW=<actual workflow>
BOT_VERSION=<actual bot version>
ENVIRONMENT=prod
```

| Field | How to choose it |
| --- | --- |
| SCRIPT_PATH | Verify the bot's root launcher; use the demonstrated `bot.py` when present |
| PYTHON_PATH | Bundle-relative Windows virtual-environment interpreter |
| PARTNER_NAME / AUTOMATION_NAME | Actual pharmacy and bot display names |
| BOT_PMS | Actual PMS; kroll for the demonstrated Kroll workflow |
| BOT_CUSTOMER / BOT_FLOW | Preserve existing telemetry identifiers or derive from verified bot configuration |
| BOT_VERSION | Existing configured/project version, not a guessed release |
| ENVIRONMENT | prod for the requested production migration |

The user's Pace example is not a universal default. Do not overwrite a pharmacy's existing secrets with another pharmacy's values.

For missing `GITHUB_TOKEN`, ask where the approved token is stored or which vault item to use; ask for its location, not its value in chat. If the user says to skip or that it is unnecessary, continue with:

```env
# GITHUB_TOKEN placeholder; omitted at the user's direction.
GITHUB_TOKEN=
```

Use an empty placeholder, not a fake nonempty token or a token borrowed from another pharmacy. Do not claim remote dependency installation passed without testing it separately.

Use a dotenv-aware editor or equivalent careful update that preserves unrelated settings and handles Windows backslashes. Compare parsed original and prepared values privately: only the intended keys should differ. Report key names and nonsecret deployment metadata, not secret values.

### Inventory required files and protect local copies

Inspect the source for environment loading, credential filenames, and resource access. Compare code expectations, the Git-tracked inventory, and the active remote files. Useful scoped searches include:

```sh
rg -n --glob '*.py' 'load_dotenv|os.getenv|os.environ|service_account|resources|\.json' .
git ls-files
```

These commands inspect source and tracked paths; do not broadly print secret file contents. Create an inventory recording local source, intended bundle-relative path, and whether each file is tracked or external. Include nested resources and every required non-repository configuration file. Exclude caches, virtual environments, logs, source prescriptions, and processing/recovery ledgers from deployment uploads.

Keep the prepared `.env` and credential files locally at their intended runtime paths and ignored/untracked. Check the exact paths with `git check-ignore` and `git ls-files -- <path>`. Use existing ignore rules; add exact filenames to local `.git/info/exclude` when a local rule is sufficient, or update shared `.gitignore` when the repository requires it. Do not blanket-ignore all JSON files. Ignoring an already tracked secret does not remove it from Git; handle that as a real issue before committing.

Protect secret copies with restrictive local permissions. Validate credential JSON structure privately without printing its contents. Replace obsolete absolute machine-specific credential paths with the intended bundle-relative paths, such as `GOOGLE_SERVICE_ACCOUNT_FILE=rx-works.json` when uploaded at root. Confirm the code resolves that path correctly.

### Upload to the matching license

1. Open External Files. Search its license selector by **license code or pharmacy name**, not numeric record ID. Verify the selected pharmacy and bot.
2. Use **Files** mode for root `.env` and individual credentials. Leave **Path Prefix** empty for root files.
3. Use **Folder** mode for the resources directory. Select that directory and accept Chrome's folder-selection prompt. The example retained paths `resources/README.md` and `resources/patient_not_found.png`.
4. For individual files needed below root, use the exact relative Path Prefix. Avoid adding `resources/` twice when folder selection already preserves it.
5. Inspect **Selected Files** before submission: correct names, relative paths, count, and sizes. Do not select the whole project, Engine folder, or a directory containing prescription data.
6. Click the page's **Upload** button. Selecting files or accepting Chrome's folder prompt alone does not complete the upload.
7. Require the success message and inspect **Uploaded Files**. Compare the inventory against code expectations and local preparation. Resolve omissions or wrong prefixes before syncing.

Respect limits reported by the current UI. If an intended file already exists, establish whether this is an authorized replacement and verify the resulting path; do not silently delete unrelated files.

**Completion evidence:** the customized environment preserves original secrets; all required external files are local and ignored; the correct license lists the expected relative paths, including the resource folder.

## 5. Sync the bundle and verify the published commit

Before syncing, confirm the dependency PR merged, the intended code is on main, and the final external-file inventory is complete.

Click `SYNC NOW` in the selected license's External Files page. The UI may show a disabled `SYNCING…` control and `Cloning and building the bundle… Sync started.` A first accessibility snapshot may still show the previous state; refresh the observation before deciding the request failed.

Wait for the final result. If progress remains stale, refresh the page and inspect the persisted status. Do not repeatedly submit builds. Require `Bundle created` or equivalent explicit success, then verify:

- The selected license still matches the pharmacy and bot.
- **BRANCH** is main.
- **COMMIT** matches the expected published main commit; compare the shown short SHA to the full verified SHA.
- The external-file inventory remains complete.

If the Hub reports an error, inspect the actual error and address its stated cause. A cloned repository, old successful bundle, or started sync does not prove this build succeeded. If the commit is unexpected, check current origin/main and saved repository/branch configuration before acting.

After any later published code or dependency change, repeat the sync and verify the new commit. Do not leave the user with a bundle built from the earlier commit.

**Completion evidence:** a successful current bundle from the intended main commit. This confirms the Hub build, not remote installation or execution.

## 6. Return the license code

Return the verified actual license code/key in a copyable form and briefly state the successful bundle result. Do not substitute the numeric database record ID. Example format using a non-real placeholder:

```text
License code: <verified-license-code>
Bundle synced from main at <verified-commit>.
```

The user enters the code in the Hub/Engine and runs the bot. This handoff completes the demonstrated procedure. Do not independently launch the production workflow or claim remote activation, dependency installation, or execution succeeded unless separately observed.

## UI recovery

Use current labels, roles, and fresh accessibility observations, not hard-coded element numbers or coordinates from the example. Reuse the correct Chrome tab. If tab control is unavailable, use native Chrome through the computer-use tool.

After an action, inspect the resulting state. Native clicks can produce a delayed visible update. If capture or selection becomes stale, reconnect to Chrome and inspect the current state before retrying; do not assume a failed observation means the action failed.

In a macOS chooser, Go to Folder (`super+shift+g`) can navigate to an absolute file or directory path, including hidden `.env`. Verify the selected filenames and enabled Open/Upload button. Reopen a stale chooser after changing the underlying file. Hand off only a concrete unresolved selection blocker, identifying the prepared files and destination.

Read current Hub documentation or deployed Engine source when an implementation detail needs verification. Older guides can differ from current behavior; do not copy obsolete package configuration. Keep routine progress updates concise and report concrete blockers rather than an unverified success.
