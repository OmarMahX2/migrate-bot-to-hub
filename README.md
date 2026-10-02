# TJM Bot Hub Migration Skill

Reusable instructions for migrating a TJM pharmacy bot to the Hub and handing the verified license code back to the user.

## Workflow

1. Find or create the correct license.
2. Refresh the Kroll dependency from dev, run the required uv commands, and publish the bot to main.
3. Configure the license in GitHub mode.
4. Retrieve and customize the remote environment, then upload required external files and resource folders.
5. Sync the bundle and verify its branch and commit.
6. Return the license code for the user to run the bot.

Every migration PR must have at least five words in its title and an empty description.

## Use

Place this repository's contents in your personal skills directory under `migrate-bot-to-hub`, preserving the root `SKILL.md`, `agents/`, and `references/` layout.

Invoke `$migrate-bot-to-hub` with the pharmacy and bot to migrate. Read [SKILL.md](SKILL.md) for the complete procedure and [the Rx Works reference](references/rx-works.md) for the verified example.

The procedure requires Git, the GitHub CLI, uv, access to the bot repository and TJM Hub, computer-use controls for Chrome, and the installed [TJM Connect Files skill](https://github.com/OmarMahX2/tjm-connect-files) for remote file retrieval.

This repository contains instructions and generalized examples. Runtime credentials, environments, real license codes, and prescription artifacts belong outside it.
