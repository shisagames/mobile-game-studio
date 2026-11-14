# Mobile Game Studio: Claude Code Plugin

**A Claude Code plugin by ShisaGames.** It guides a Unity mobile game (iOS and Android) from **the idea to the store and live ops**. It packs the process from *Lemonade Rush: Bottle Sort*, which ShisaGames took from an empty project to the App Store and Google Play in 22 days, into a form any project can reuse.

> **Status:** v1.0.0, public (November 2026). The guide and notes are in Turkish, the plugin and skill descriptions are in English.
> **Licence:** ShisaGames Free Use License. Free to use, including for commercial games; no resale or redistribution. See [LICENSE](LICENSE).

## What it does

The plugin includes the `mobile-game-studio` skill. The skill works out which stage a project is in and loads only the guide section that stage needs:

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

## Repository layout

```
mobile-game-studio/
├── .claude-plugin/
│   ├── plugin.json                  # Plugin manifest (name, version, author: ShisaGames)
│   └── marketplace.json             # "shisagames" marketplace, so the plugin installs with /plugin
├── skills/
│   └── mobile-game-studio/
│       ├── SKILL.md                 # Working rules, stage → reference map, idea evaluation and GDD flows
│       └── references/
│           ├── playbook.md          # 17-phase development guide, checklists, pitfalls, tool layers
│           ├── economy-template.md  # Lemonade Rush's real economy and mechanics values
│           ├── asset-pipeline.md    # Art direction, code drawing pipeline, plugins and libraries
│           └── competitor-analysis.md  # Magic Sort, Block Out, Gossip Harbor, Smash Fest + idea checklist
├── LICENSE                          # ShisaGames Free Use License
└── README.md
```

## Installation

The repository is public; no GitHub account or access is needed. The repository address is `shisagames/mobile-game-studio`.

**As a plugin (recommended):** in Claude Code:

```
/plugin marketplace add shisagames/mobile-game-studio
/plugin install mobile-game-studio@shisagames
```

To update: `/plugin marketplace update shisagames`.

**From a local copy (for development):** after cloning the repository:

```
/plugin marketplace add /path/to/mobile-game-studio
/plugin install mobile-game-studio@shisagames
```

**As a skill only:** copy or link `skills/mobile-game-studio` into `~/.claude/skills/`. If you also install the plugin, don't add the skill a second time; the same skill would load twice.

## Usage

Claude Code picks the skill on its own from what you write. Example prompts:

- "Yeni bir oyun fikrim var: ... Bu fikri değerlendir."
- "Bu oyunun ekonomisini tasarlayalım."
- "Sanat yönü için mockup turu başlat."
- "Mağazaya çıkış için neler kaldı?"
- "Sırada ne var?"

As a plugin, call it directly by name: `/mobile-game-studio:mobile-game-studio`. Installed as a skill only, it's `/mobile-game-studio`.

## Recommended tools

| Layer | Tool |
|---|---|
| Unity | Unity's official Claude Code plugin (Unity 6+, `com.unity.pipeline` + Unity CLI) |
| Design | Figma plugin, Miro and 231st UI Kit connectors |
| Assets | SpriteCook, CellCog, Polyfork (check the licence) |
| Store | App Store Connect API script, or a community MCP with the lowest-permission key |
| Post-launch | Firebase (Analytics + Crashlytics) or Unity Gaming Services |
| Other | security-audit, deep-research, skill-creator |

Details: `skills/mobile-game-studio/references/playbook.md` §8 and `asset-pipeline.md` §4.

## Contributing

When a new project teaches a general lesson:
1. Add it to the pitfalls table in `playbook.md` §5 (symptom → cause → fix).
2. If an economy value changed with data, update `economy-template.md` and note where the data came from.
3. Raise `version` in `.claude-plugin/plugin.json` (patch for content fixes, minor for new sections).
4. Use short commit messages that describe what changed.

**Never put these in this repo:** API keys, tokens, keystore details, team IDs, personal addresses or phone numbers. Those live in each project's git-ignored `.env`.

## Licence

© 2026 ShisaGames. Licensed under the **ShisaGames Free Use License**:

- ✅ Install and use it for free, including on commercial games.
- ✅ Change it for your own projects.
- ✅ What you make with it (games, documents, code) is yours.
- ❌ Selling it, republishing or redistributing it as your own plugin, skill, template or course needs written permission. Sharing a link to this repo is always fine.

Full text: [LICENSE](LICENSE).

The analysis in `competitor-analysis.md` relies on public store pages and industry reports. Names, trademarks and figures belong to their owners and are for reference only.
