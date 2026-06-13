# StudyLog AI

**StudyLog** is a spaced-repetition study application that implements the **SM-2 algorithm** (the same algorithm Anki uses) for scheduling review intervals, augmented with a knowledge graph that links related concepts. It runs as a Cloudflare Worker serving a study dashboard with review queues, concept maps, and activity heatmaps.

## Why It Matters

The Ebbinghaus forgetting curve shows that without review, retention drops to ~40% within 4 days. Spaced repetition exploits the spacing effect: reviewing material at increasing intervals flattens the forgetting curve, producing durable long-term memory with minimal study time. SM-2 is the gold-standard algorithm — simple, effective, and battle-tested by millions of Anki users. StudyLog wraps SM-2 with a concept graph so learners see *connections* between cards (how glycolysis links to the citric acid cycle), not just isolated facts. This addresses a known limitation of flashcard systems: they teach discrete facts but not structural understanding.

## How It Works

### SM-2 Algorithm

Each card has an **ease factor** E (initially 2.5) and an **interval** I. After each review, the user rates recall quality q ∈ {0, 1, 2, 3, 4, 5}:

```
if q < 3:
    I = 1                    // failed — reset
else:
    E = E + (0.1 - (5-q) * (0.08 + (5-q) * 0.02))
    E = max(1.3, E)          // ease floor

    if n == 0: I = 1
    elif n == 1: I = 6
    else: I = round(I * E)   // n = successful review count
```

Next review date = today + I days. Easy cards (high q) get exponentially growing intervals; hard cards stay near I=1. The ease factor adapts per-card, ensuring difficult material gets more attention.

### Complexity

- Review scheduling: O(1) per card
- Review queue selection: O(N log N) to sort by due date (N = total cards)
- Concept graph traversal: O(V + E) for BFS/DFS

### Knowledge Graph

Cards are linked in a directed graph:

```
Card("Glycolysis") → Card("Pyruvate") → Card("Acetyl-CoA") → Card("Citric Acid Cycle")
```

When a user fails a card, StudyLog surfaces the card's neighbors — the prerequisite knowledge that might be weak. The graph visualization helps learners see the topology of what they know and what they're missing.

### Weakness Detection

The algorithm tracks per-card success rate:

```
weakness(card) = 1 - (successes / reviews)
```

Cards with weakness > 0.5 are flagged for priority review. Concept-level weakness aggregates all cards linked to a concept:

```
weakness("enzyme kinetics") = mean(weakness(card) for card in concept("enzyme kinetics"))
```

## Quick Start

```bash
# Deploy as Cloudflare Worker
npx wrangler deploy

# Local development
npx wrangler dev
```

The worker serves a dashboard with: today's review queue, concept graph (SVG), review heatmap (GitHub-style), and deck statistics.

## API

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/` | GET | Full study dashboard with review queue, concept graph |

*Client-side: localStorage for card data, SM-2 scheduling, concept graph rendering.*

## Architecture Notes

StudyLog maps to γ + η = C in education: γ (gamma) is the constructive learning — building new memories through review. η (eta) is the forgetting that spaced repetition fights — each review prevents the memory from decaying. The SM-2 algorithm optimizes the timing of γ (when to construct/reinforce) based on the rate of η (how fast the memory is decaying). Their balance produces C (competence/knowledge). The concept graph adds structural γ — understanding relationships between facts, not just the facts themselves. See [ARCHITECTURE.md](https://github.com/SuperInstance/SuperInstance/blob/main/ARCHITECTURE.md).

## References

1. Wozniak, P. (1985). *Optimization of Learning*. University of Technology in Poznan. — Original SM-2 algorithm description.
2. Ebbinghaus, H. (1885). *Memory: A Contribution to Experimental Psychology*. — The forgetting curve and spacing effect.
3. Cepeda, N. J., et al. (2006). "Distributed Practice in Verbal Recall Tasks: A Review and Quantitative Synthesis." *Psychological Bulletin*, 132(3), 354–380.

## License

MIT
