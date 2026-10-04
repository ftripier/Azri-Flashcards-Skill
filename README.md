# Azri flashcards

An [Agent Skill](https://agentskills.io) for drafting flashcards, reviewing them in chat, and uploading the approved cards to Azri through MCP.

## Install

Install with the [`skills` CLI](https://github.com/vercel-labs/skills). You need Node.js and npm to run `npx`.

### From a local checkout

Run this from the project where you want to use the skill, replacing the path with your checkout:

```sh
npx skills add /path/to/azri-flashcards --skill azri-flashcards
```

The installer prompts for the target agent and installation method. Add `--global` to make the skill available across projects, or `--agent codex` / `--agent claude-code` to select an agent.

### From GitHub

Once this repository is pushed to GitHub, replace `OWNER/REPO` with its actual owner and repository name:

```sh
npx skills add OWNER/REPO --skill azri-flashcards
```

This repository keeps `SKILL.md` at the root, a layout supported by the CLI. Publishing the repository makes it installable; no npm package or build step is required.

## Connect Azri

Configure and authenticate the Azri MCP server in your agent before uploading cards. The skill expects these operations:

- `azri_list_decks` to find a destination.
- `azri_create_deck` to create an approved new deck.
- `azri_create_cards` to upload approved cards.

Tool prefixes can vary by agent. Installing this skill does not configure the MCP connection or supply credentials. Without the connection, the agent can still draft and review cards in chat.

## Use

Ask your agent:

> Use azri-flashcards to draft 10 introductory flashcards about matrix rank for a Linear Algebra deck.

The skill shows every proposed front and back and names the destination deck. After you approve the exact cards and destination, it uploads them and reports the actual result.

## Verify discovery

From this repository, list the available skill without installing it:

```sh
npx skills add . --list
```

The output should include `azri-flashcards`. The complete agent instructions are in [SKILL.md](SKILL.md).
