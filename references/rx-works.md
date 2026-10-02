# Rx Works migration reference

Verified October 2, 2026. These identities, paths, commits, and UI observations explain the demonstrated workflow; they are not defaults for future pharmacies. Real license codes and credentials are intentionally omitted. Machine-specific paths and database identifiers are generalized for publication.

## Contents

- [License and repository](#license-and-repository)
- [Exceptional Git history](#exceptional-git-history)
- [Locate the active remote bot](#locate-the-active-remote-bot)
- [Environment and upload inventory](#environment-and-upload-inventory)
- [Final verified result](#final-verified-result)
- [Previously inspected technical references](#previously-inspected-technical-references)

## License and repository

- Pharmacy: Prescription Works; Hub bot: Data Entry; division: CANADA.
- Repository: `https://github.com/tjmlabs/Prescription-Works-canada-bot-kroll.git`.
- Local repository example: `<local-projects>/Prescription-Works-canada-bot-kroll`.
- Global search `prescription` found an existing matching license, initially Regular and Under Development, without a machine or expiry. No duplicate was created.
- The existing license was updated to GitHub mode with the HTTPS clone URL. Its sync interval stayed Every 24 hours. The form populated a required one-year expiry.
- No branch selector appeared in Edit. The completed sync confirmed main, so explicit branch-field editing was not necessary to establish this result.
- External Files search by numeric record ID returned no results; searching Prescription Works selected the correct license. License-code search is also supported by the user-directed workflow.

## Exceptional Git history

The intended latest bot implementation was `feat/drop-off` at `f23749b`. Its history was unrelated to main and development; the repository was not shallow. A temporary `chore/sync-main` branch started from the chosen implementation and reconciled main ancestry while retaining the selected tree:

```sh
git merge --allow-unrelated-histories -s ours origin/main -m 'Sync Rx Works'
```

This was appropriate only after establishing that the chosen implementation should replace the old main tree. It deliberately retained no content from the old main tree. Do not reuse this command for ordinary merges, uncertain source selection, or conflicts containing needed changes. Verify both ancestry and the resulting tree before creating the PR.

[PR #5](https://github.com/tjmlabs/Prescription-Works-canada-bot-kroll/pull/5) published that exact implementation to main at `ff0b4d6`. All 9 existing offline tests passed. The original PR title was short because the five-word requirement was supplied later; it is historical evidence, not an approved future title example.

The Kroll source initially referenced `feat/popups-extension`. The user supplied the dependency rule after the first sync: the reference in the bot's TOML must use **dev**, while the bot deploys from **main**. All three required commands succeeded and resolved Kroll 2.0.0 at `6774ba7194dcbf5dd9f5b7417fb32e5c82fd2900`:

```sh
uv sync
uv sync --upgrade-package kroll-automation
uv lock
```

[PR #6](https://github.com/tjmlabs/Prescription-Works-canada-bot-kroll/pull/6) merged only `pyproject.toml` and `uv.lock` into main at `02439df4edb60cb2997290fee57ed8ce5cf0466d`. The 9 offline tests passed again. Both demonstrated PR descriptions were empty. Both titles predated the five-word rule. Future migrations must prepare dependencies before the initial PR, use at least five title words, and keep the description empty.

## Locate the active remote bot

The saved TJM Labs Connect device was `Rx Works`, profile labs. Its Engine directory followed the pattern `C:\Users\<remote-user>\Desktop\TJM Engine`. Its `.env` was separate Engine configuration and was not used as the bot environment.

The Engine settings database identified:

| Setting | Example value (paths generalized) |
| --- | --- |
| automation_name | prescription-works-bot |
| partner_name | Prescription Works |
| bot_script_path | `C:\Users\<remote-user>\TJM Bot Project\Prescription-Works-bot\bot.py` |
| python_exec_path | The bot directory's `.venv\Scripts\python.exe` |
| secrets_file_path | Empty |

The active bot directory contained `.env`, `rx-works.json`, source, resources, the virtual environment, and runtime folders. Only required distributable configuration and assets were fetched for upload. Investigation copies were stored outside the repository with restrictive permissions. Existing local runtime logs were moved to external artifact storage without discarding their contents or creating an empty replacement ledger.

## Environment and upload inventory

The bot's original secrets and unrelated runtime values were retained. The customized deployment metadata was:

```env
SCRIPT_PATH=bot.py
PYTHON_PATH=.venv\Scripts\python.exe
PARTNER_NAME=Prescription Works
AUTOMATION_NAME=Prescription Works Bot
BOT_PMS=kroll
BOT_CUSTOMER=prescription-works
BOT_FLOW=prescription
BOT_VERSION=0.1.0
ENVIRONMENT=prod
GOOGLE_SERVICE_ACCOUNT_FILE=rx-works.json
GITHUB_TOKEN=
```

The user explicitly said to skip the GitHub token for this bot. The empty placeholder had an explanatory comment. It did not establish successful remote private-package authentication.

The absolute remote service-account path was replaced with `rx-works.json` because the credential would be at the bundle root. The JSON was validated privately as a service account. `.env` was already ignored; the exact `/rx-works.json` filename was added to local `.git/info/exclude`. Both files were untracked and protected locally.

The remote resources directory contained README.md and patient_not_found.png. The remote image's bytes matched the local repository image. Folder mode preserved the directory prefix. Files mode with an empty Path Prefix uploaded the environment and JSON at root. The verified Hub inventory was:

| Bundle path | Upload mode |
| --- | --- |
| `.env` | Files, empty prefix |
| `rx-works.json` | Files, empty prefix |
| `resources/README.md` | Folder |
| `resources/patient_not_found.png` | Folder |

Chrome's native chooser briefly left Open disabled. Reconnecting to Chrome and reopening selection resolved it. Go to Folder selected the hidden environment by its absolute path. Chrome's folder prompt selected two resources; the page's own Upload button then completed the upload. Selected Files and Uploaded Files were separate checkpoints.

## Final verified result

The first bundle used main at `ff0b4d6`. After the dependency correction merged, resync reported `Sync finished. Bundle created.`, branch main, and commit `02439df`. All four external files remained listed. The user then requested the license code as the final handoff. Remote activation and execution were not performed or verified.

## Previously inspected technical references

Use these only when the implementation detail is relevant, and inspect current/deployed versions:

- [Engine migration guide, dev](https://github.com/tjmlabs/TJM_Engine/blob/dev/TJM_ENGINE_MIGRATION_GUIDE.md)
- [Engine migration guide, main](https://github.com/tjmlabs/TJM_Engine/blob/main/TJM_ENGINE_MIGRATION_GUIDE.md)
- [Engine entry-point tests](https://github.com/tjmlabs/TJM_Engine/blob/dev/tests/test_script_path_resolution.py)
- [Engine dependency setup](https://github.com/tjmlabs/TJM_Engine/blob/dev/bundle/venv_setup.py)

The demonstrated guide versions differed in troubleshooting coverage, and Engine code differed from guide assumptions about compilation. Do not infer current behavior from a stale guide.
