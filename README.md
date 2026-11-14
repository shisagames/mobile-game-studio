# Mobile Game Studio: Claude Code Skill

A Claude Code skill that guides a Unity mobile game (iOS and Android) from **the idea to the store and live ops**. It packs the process from *Lemonade Rush: Bottle Sort*, which ShisaGames took from an empty project to the App Store and Google Play in 22 days, into a form any project can reuse.

> **Status:** private, first version (November 2026). The guide and notes are in Turkish, the skill description is in English.

## What it does

The skill works out which stage a project is in and loads only the guide section that stage needs:

| Stage | What the skill does |
|---|---|
| **Idea** | Takes the idea through the new-idea template, matches it to a strategy pattern and its closest hit game, scores it out of 56 on a 28-item checklist, then proposes features to borrow, risks and 3–5 ad creative concepts |
| **GDD and plan** | One-page GDD (core loop, stuck moment, boosters, obstacle pipeline, meta, monetization), feature IDs, production order |
| **Prototype** | Pure C# rules with EditMode tests, UI built in code, level generator and solver |
| **Economy** | Starting values from Lemonade Rush: coins, boosters, unlock schedule, difficulty curve, Daily Gift, Road Pass, League, Race, cosmetic price ladder, IAP catalogue, ad rules |
| **Art** | Where drawing in code works and where it doesn't, the SVG/Python sprite pipeline, mockup rounds, when to use SpriteCook, CellCog or Polyfork |
| **Release** | Signing via `.env`, Unity IAP 5, LevelPlay, KVKK/GDPR consent flow, account deletion, achievements, security audit |
| **Store** | ASO with `listing.json`, store images rendered in code, the iOS archive and signing recipe, TestFlight, Play closed test (12 testers / 14 days), App Review |
| **Post-launch** | Firebase behind consent, soft launch plan, diagnostics, live ops |

It also includes a **pitfalls table** (25 problems we hit, with cause and fix) and **checklists** for before a build, after an upload and on launch day.

## Files

```
mobile-game-studio/
├── SKILL.md                         # Main file: working rules, stage → reference map, idea evaluation and GDD flows
└── references/
    ├── playbook.md                  # 17-phase development guide, checklists, pitfalls, tool layers
    ├── economy-template.md          # Lemonade Rush's real economy and mechanics values
    ├── asset-pipeline.md            # Art direction, code drawing pipeline, plugins and libraries
    └── competitor-analysis.md       # Analysis of Magic Sort, Block Out, Gossip Harbor and Smash Fest + idea checklist
```

## Installation

Clone it into the global skills folder of whichever machine will use it:

```bash
git clone https://github.com/uygurberkay/mobile-game-studio.git ~/.claude/skills/mobile-game-studio
```

The skill loads on its own in a new Claude Code session. To use it in a single project only, clone it into that project's `.claude/skills/mobile-game-studio` folder instead.

To update:

```bash
git -C ~/.claude/skills/mobile-game-studio pull
```

## Usage

Claude Code picks the skill on its own from what you write. Example prompts:

- "Yeni bir oyun fikrim var: ... Bu fikri değerlendir."
- "Bu oyunun ekonomisini tasarlayalım."
- "Sanat yönü için mockup turu başlat."
- "Mağazaya çıkış için neler kaldı?"
- "Sırada ne var?"

You can also call it directly by name: `/mobile-game-studio`.

## Recommended tools

| Layer | Tool |
|---|---|
| Unity | Unity's official Claude Code plugin (Unity 6+, `com.unity.pipeline` + Unity CLI) |
| Design | Figma plugin, Miro and 231st UI Kit connectors |
| Assets | SpriteCook, CellCog, Polyfork (check the licence) |
| Store | App Store Connect API script, or a community MCP with the lowest-permission key |
| Post-launch | Firebase (Analytics + Crashlytics) or Unity Gaming Services |
| Other | security-audit, deep-research, skill-creator |

Details: `references/playbook.md` §8 and `references/asset-pipeline.md` §4.

## Contributing

When a new project teaches a general lesson:
1. Add it to the pitfalls table in `references/playbook.md` §5 (symptom → cause → fix).
2. If an economy value changed with data, update `economy-template.md` and note where the data came from.
3. Use short commit messages that describe what changed.

**Never put these in this repo:** API keys, tokens, keystore details, team IDs, personal addresses or phone numbers. Those live in each project's git-ignored `.env`.

## Licence

Private, ShisaGames internal use. The analysis in `competitor-analysis.md` relies on public store pages and industry reports. Names and figures belong to their owners and are for reference only.
