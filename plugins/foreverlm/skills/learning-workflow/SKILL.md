---
name: foreverlm-learning-workflow
description: Use ForeverLM to plan study, load private source material, conduct a review, and save the review transcript.
---

# ForeverLM learning workflow

Use the ForeverLM MCP server for every library, Project, Schedule, and review operation. Never infer stored state or substitute direct database access when the connector fails.

1. Call `get_mcp_status` before starting a ForeverLM workflow. If no authorized hosted connection or active Mac relay is available, or the connector returns an error, explain the connector failure instead of inventing library state.
2. For "today" requests, call `get_learning_journey`, then `get_today_sources` when source text is needed.
3. Keep planning changes explicit. Identify the journey and source IDs before assigning, moving, completing, reopening, or removing work.
4. For a review, call `start_review_session`, teach interactively with the learner's sources, and call `end_review_session` with the exported transcript, summary, score, and source IDs. Do not claim that a review was saved until that call succeeds.
5. Ask for confirmation immediately before destructive changes such as clearing a day or deleting a journey.
6. Treat connector results as discovery, not proof that a reading is external. After `read_slack_channel`, use every `library_matches` source ID with `get_source_content` when the learner asked to read or discuss it. For each named reading without an exact match, search ForeverLM by its DOI or title before saying it is only a link or attachment.

ForeverLM keeps the library in the user's private iCloud container. An authorized always-on MCP host can serve the public study surface while the desktop app is closed; native-only capabilities still require an active Mac relay.
