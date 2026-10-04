---
name: azri-flashcards
description: Draft flashcards from a topic or supplied material, show them for review, and upload approved cards to Azri using its MCP tools. Use when the user wants to make or revise flashcards for Azri.
---

# Azri flashcards

Follow this sequence: draft, show for review, wait for approval, then upload using Azri MCP.

## Requirements

Uploading requires an authenticated Azri MCP connection in the host agent. Installing this skill supplies instructions only; it does not install or configure the MCP server. Drafting and review can proceed without the connection.

## Draft and review

- Use the user's topic, source material, language, level, and requested count. If unspecified, start with 10 cards at an introductory level and state that assumption. Ask for a topic if none is available.
- Test one concept per card. Write a clear recall question or single term on the front and the minimum sufficient answer on the back. Avoid ambiguous prompts, large lists, and unsupported facts. Follow supplied material; verify uncertain or time-sensitive claims with available sources before including them.
- Every card appears independently in spaced repetition. Include the subject and enough context in each front to make it understandable without the deck title, neighboring cards, or this conversation. For example, write “In linear algebra, what is the rank of a matrix?” rather than “What is its rank?” Check reverse-card prompts independently too; each direction must identify its subject without revealing the answer.
- Show every proposed front and back in a numbered Markdown table or list in the conversation. “Post for review” means display here, not send to an external service.
- Name the target deck in the preview. Use a user-specified deck, or propose a topic-based name. Use `azri_list_decks` when needed to resolve an existing deck; do not create a deck yet. Make any proposed new deck explicit.
- Default to one-directional cards. If proposing linked reverse cards, show both directions and include both in the total for review.
- Ask explicitly whether to upload this exact set to the named deck, including creation of a new deck if proposed. Then stop and wait for the user's response. The initial request to make or upload flashcards is not approval of an unseen draft.
- Apply requested edits and show the revised cards for approval before uploading them. Honor approval of a clearly identified subset. Do not silently add, rewrite, or reverse cards after approval. If the approved version or destination is unclear, resolve it before writing.

## Upload approved cards

Use the connected Azri MCP tools. Tool prefixes vary between hosts; discover tools by the operation names below and inspect their current schemas. Do not invent tool arguments or substitute another service. If Azri is unavailable or authentication fails, keep the draft in the conversation and explain that the Azri MCP connection is needed to upload it. Do not claim success.

1. Resolve the approved deck. Use `azri_list_decks` for ambiguous names, following pagination if needed. Pass either `deck_id` or `deck_name` to `azri_create_cards`. If the approved destination requires a new deck, call `azri_create_deck` after approval and use its returned ID.
2. Call `azri_create_cards` with exactly the approved fronts and backs. Its current limits are 1–50 cards per call, front length 1–2000 characters, and back length 1–5000 characters. Linked reverse cards count toward the 50-card limit. Split larger sets into batches without requesting approval again for unchanged, already-approved cards. If a card exceeds a limit, show a revised version for approval rather than silently truncating it.
3. Set `on_duplicate` to `skip` unless the user explicitly approved duplicates. Use a fresh UUID v4 `idempotency_key` for each batch and retain it with that batch's payload. On a transient failure, retry at most once with the same key and unchanged payload; the current key cache lasts 24 hours. If the outcome remains uncertain, stop and report it instead of retrying with a new key. Do not resend successful batches.
4. Read the tool responses. Report the deck, number actually created, and any skipped cards or failures. For partial completion, distinguish uploaded cards from those still pending. Do not report the requested count as the created count without checking the result.

Approval is scoped to the reviewed cards and destination. It does not authorize deleting cards, overwriting existing content, or changing unrelated decks.
