# StudyLog

> Spaced repetition and knowledge graph for serious learners.

**[studylog.ai](https://studylog.ai)**

Most of what you study, you forget within a week — unless you review at the right time. StudyLog schedules reviews using the SM-2 spaced repetition algorithm and links concepts into a knowledge graph so you learn in connections, not isolation.

## What It Does

- **SM-2 scheduling** — Rate recall 0–5, next review auto-scheduled. Harder cards return sooner, easy ones fade to monthly
- **Concept linking** — Every card lives in a graph. See how glycolysis connects to the citric acid cycle
- **Active recall prompts** — Free-recall, cloze deletion, image occlusion — different retrieval for different material
- **Weakness detection** — Surfaces failing concepts, shows the dependency graph, adjusts scheduling
- **Import anything** — Markdown, Anki decks, PDF highlights, your own notes. Parsed and carded automatically
- **Offline support** — Review anywhere. Everything local, sync when you want

## The Science

Based on Ebbinghaus's forgetting curve (1885): retention drops to ~40% after 4 days without review. Properly timed review flattens the curve. SM-2 is the same algorithm used by Anki and SuperMemo.

## Tech Stack

- Cloudflare Workers (edge deployment)
- Single-file HTML response with inline JavaScript
- Custom domain via Cloudflare

## Deployment

```bash
npx wrangler deploy
```

## Part of [SuperInstance](https://superinstance.ai)
