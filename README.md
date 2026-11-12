# Werewolf Agent 🐺

A rule-based AI agent that plays **Werewolf** (the social deduction game) — built for the [make.fun](https://make.fun) `werewolf-agent-01` competition.

No LLM calls. No API keys. Pure Python 3 stdlib, fully deterministic.

## How it plays

8 players: 2 werewolves, 1 seer, 1 doctor, 4 villagers. 30 games per trial (15 as wolf, 15 as village) against 7 house players.

The agent parses the full public log every decision into per-seat suspicion scores, then acts by role:

- **Seer** — checks the most suspicious unchecked player each night, reveals a wolf the moment one is found. Never announces "not a wolf" clears (that gets the seer killed).
- **Doctor** — protects the seer claimant first, then the loudest accuser.
- **Werewolf** — matches its partner's kill, kills seer claimants first, votes the most exilable villager by day, and buses a doomed partner only.
- **Villager** — votes the top suspect on real evidence; abstains on weak evidence instead of exiling a random villager — except at ≤5 players alive, where the village must take its best shot.

Suspicion scoring: votes, exiles, accusations, and seer claims are weighted by day and by whether the accused turned out to be a wolf. Proven liars are ignored. Seer claims are first-class evidence.

## Results (local practice trials)

| Trial | Score | Wolf | Village |
|---|---|---|---|
| 0 | 667 | 13/15 | 7/15 |
| 1 | 467 | 12/15 | 2/15 |
| 2 | 633 | 13/15 | 6/15 |
| **Mean** | **589** | **38/45** | **15/45** |

House baseline: **367**. The agent beats it on every trial.

Wolf play is the strength (38/45). Village play is the known weakness — the agent trusts seer claims that sometimes turn out to be wolf bluffs.

## Run it

```bash
# needs the make.fun kit env (MAKEFUN_PROMPT_FILE, MAKEFUN_TRIAL_SEED, MAKEFUN_ENV_URL)
python3 -P -s main.py
```

Stdlib only — no dependencies to install. Deterministic: same seed, same game.

## Structure

- `main.py` — the whole agent (609 lines): xoshiro256** RNG, game-state model, log parsing, suspicion scoring, per-role logic, decision loop, env glue (`POST /observe`, `/act`).

## Author

Built by **Kei 🌸** for **bobonsam** — [bobonsam.github.io](https://bobonsam.github.io)

Originally written for the make.fun Werewolf bounty. The entry wasn't submitted (the competition's transcript/bundle authorship gate blocked it), so it's published here as a portfolio project.
