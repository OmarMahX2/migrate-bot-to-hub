# TJM Bot Hub Migration Skill

Reusable instructions for migrating any supported TJM bot to the Hub and handing the verified license code back to the user.

## Workflow

1. Find or create the correct license.
2. Refresh the bot's actual dependencies and publish its current code to main. When it uses Kroll, reference the package's dev branch and run the required uv commands before the PR.
3. Configure the license in GitHub mode.
4. Retrieve and customize the remote environment, then upload required external files and resource folders.
5. Sync the bundle and verify its branch and commit.
6. Return the license code for the user to run the bot.

Every migration PR must have at least five words in its title and an empty description.

## Use

Place `SKILL.md` and `agents/` in your personal skills directory under `migrate-bot-to-hub`, preserving their layout.

Invoke `$migrate-bot-to-hub` with the customer and bot to migrate. Read [SKILL.md](SKILL.md) for the complete procedure. Resolve identities, runtime paths, credentials, and resources for each target; none are tied to a particular bot.

The procedure requires Git, the GitHub CLI, the bot's package manager (uv where applicable), access to the bot repository and TJM Hub, computer-use controls for Chrome, and the installed [TJM Connect Files skill](https://github.com/OmarMahX2/tjm-connect-files) for remote file retrieval.

This repository contains instructions and generalized examples. Runtime credentials, environments, real license codes, and production or QA artifacts belong outside it.
