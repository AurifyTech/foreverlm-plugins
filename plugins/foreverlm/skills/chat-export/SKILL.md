---
name: foreverlm-chat-export
description: Export an AI conversation into ForeverLM as a chat while preserving its turns and making associated Markdown documents and private-library references open natively in ForeverLM. Use when the user asks to move, import, export, or share a chat and its artifacts into ForeverLM. Never file the transcript itself as a Source.
---

# ForeverLM chat export

Keep each artifact in its native ForeverLM type: a conversation is a chat imported with `import_review_chat`, authored Markdown is a Doc, and material the learner read elsewhere is a Source. Do not flatten the conversation or its Docs into library Sources.

## Preflight the whole export

1. Call `get_mcp_status`. A Doc requires an active Mac relay. If an associated Doc cannot be created, stop before importing the chat so the export does not contain broken links.
2. Collect the completed user and assistant turns in order. Exclude system or developer instructions, tool traces, hidden reasoning, credentials, and temporary status messages. Preserve the visible wording and role labels.
3. Inventory the artifacts explicitly associated with the conversation. Treat Markdown links to local files, attached reports, and named research memos as associated only when the user put them in scope.
4. Resolve any ForeverLM Sources cited by the conversation or Docs. Search by exact title, author, URL, or DOI and confirm each chosen ID with `get_source_content`; do not guess IDs from similar titles.
5. When the learner named a destination Project, resolve its exact ID with `list_projects`. Never infer a Project from the app's current selection.

## Make associated Markdown portable first

For every associated Markdown artifact:

- Use `list_docs` before writing. Reuse an exact existing Doc when it is the same artifact; do not create title-based duplicates.
- Create a missing Doc with `create_doc`. For an existing editable Doc, call `read_doc`, compare its complete Markdown, and use `write_doc` with the returned revision only when the content differs.
- Replace clickable local-file targets in the transcript with `[label](foreverlm://doc/<doc_id>)`. Never leave `/Users/...`, `file://...`, or another machine-local target as the destination of a link in the exported chat.
- In the Doc, link confirmed private-library works as `[title](foreverlm://source/<source_id>)`. Keep external primary-literature links when they are useful; native Source links identify the copies actually used from ForeverLM.
- Call `read_doc` after the write and verify the complete body. Verify each native Source URI by reading that exact source.

## Import the chat once

Before any import, search existing review sessions by title and inspect plausible transcripts.

- If no matching chat exists, call `import_review_chat` with the complete role-labeled transcript after every link has been rewritten. Pass the confirmed `source_ids` so the imported chat retains its library associations, and pass the resolved `project_id` on the first import when the learner named a destination Project.
- If the transcript is too long for one call, append chunks only when the live tool schema exposes `session_id`. Capture the ID returned by the first chunk and pass that exact ID on every later chunk.
- If a matching completed chat already exists and is identical, make no write.
- If a matching chat needs changes but the live plugin cannot update or append to that session, stop and explain the limitation. Do not call `import_review_chat` without `session_id`, because that creates a duplicate chat. Do not try to rewrite a completed import with `end_review_session`.
- Create a replacement copy only when the user explicitly chooses that fallback after being told the existing chat will remain.

## Verify before claiming success

Call `get_review_session_transcript` on the resulting session and check turn order, visible content, Doc links, and Source associations. Confirm that every `foreverlm://doc/` URI resolves with `read_doc`, every `foreverlm://source/` URI resolves with `get_source_content`, and no clickable machine-local link remains. When a Project was requested, verify that the saved session reports that `project_id`. Report the chat ID, Project ID, Doc IDs, Source IDs, and any plugin limitation. Claim a complete export only when these checks pass.

## Share through ForeverLM only when requested

If the user also asks for a ForeverLM share link, first look for a dedicated share tool in the live MCP schema. When none exists, use the Computer Use skill to open the imported chat in the installed ForeverLM app and inspect the fresh accessibility tree for a native `Share` control.

- Open the share sheet when the control exists, but follow the Computer Use confirmation policy before any action that creates a public link or changes access.
- Verify the resulting link belongs to the intended ForeverLM chat before reporting it.
- Do not substitute `Copy conversation` for sharing; it copies transcript content and does not establish a shareable ForeverLM chat.
- If the installed app exposes no native Share control, stop and report that product limitation. Do not invent a URL or export the chat into a Source as a workaround.
