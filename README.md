# Social Media Skills

Five Claude skills for tidying up and archiving saved/shared content on **Facebook**, **Messenger** and **Threads**. Each skill is a single `SKILL.md` that tells Claude how to drive the browser (Chrome side panel), read the content, and produce an Excel file or perform a clean-up action.

## Repository layout

```
.
├── README.md                  <- this file
├── CLAUDE.md                  <- guidance for Claude when working in this repo
└── skills/
    ├── facebook-saved-categorizer/SKILL.md
    ├── facebook-unsave/SKILL.md
    ├── messenger-links-categorizer/SKILL.md
    ├── messenger-unsend/SKILL.md
    └── threads-reposts-categorizer/SKILL.md
```

## The skills

| Skill | Command | Read/Write | Purpose |
|---|---|---|---|
| [facebook-saved-categorizer](skills/facebook-saved-categorizer/SKILL.md) | `/facebook_saved_categorizer N` | Read-only | Categorizes the first N items on the Facebook Saved page into `facebook_saved_items.xlsx` (sheet "Saved items"). |
| [facebook-unsave](skills/facebook-unsave/SKILL.md) | `/facebook-unsave N` | **Changes account** | Unsaves the first N items on the Facebook Saved page, after showing the list and getting a clear yes. |
| [messenger-links-categorizer](skills/messenger-links-categorizer/SKILL.md) | `/messenger-links-categorizer 2026Q3` | Read-only | Categorizes shared links, posts and reels in the currently open Messenger chat for a quarter into `messenger_chat_items_<yyyyQx>.xlsx` (sheet "Chat items"). |
| [messenger-unsend](skills/messenger-unsend/SKILL.md) | `/messenger-unsend 2026Q3` | **Irreversible** | Unsends (for everyone) the links/posts/reels you sent in the open chat for a quarter. Only acts on items with a found URL and high categorization confidence, after confirmation. |
| [threads-reposts-categorizer](skills/threads-reposts-categorizer/SKILL.md) | `/threads-reposts-categorizer N` or `4-30` | Read-only | Categorizes reposts on your Threads Reposts tab into `threads_reposts_<handle>_<start>-<end>.xlsx` (sheet "Reposts"). |

### How they fit together

- **Categorize first, clean up second.** `facebook-saved-categorizer` pairs with `facebook-unsave` (same Saved page, same top-N positions), and `messenger-links-categorizer` pairs with `messenger-unsend` (same chat, same quarter, same classification rules).
- The categorizer skills share one Excel template: `#`, (timestamp), title/caption, page/author, URL, type, category, summary, confidence, `Detail`, and an always-empty `Note` column as the last column for your own notes.
- The destructive skills (`facebook-unsave`, `messenger-unsend`) never run without a numbered list and an explicit yes, and skip anything uncertain.

## Installing a skill

Each folder under `skills/` is a self-contained skill (folder name = skill name, `SKILL.md` with `name` and `description` front matter). To use them:

- **Claude (claude.ai / desktop):** zip a skill folder and upload it under Settings → Skills, or ask Claude to propose it as a skill.
- **Claude Code:** copy the folders into `~/.claude/skills/` (user-level) or `<project>/.claude/skills/` (project-level).

## Requirements and assumptions

- Claude with browser control in your **own signed-in Chrome** (the skills reference the `mcp__claude-device__chrome_*` tools). You must already be logged in to Facebook / Messenger / Threads.
- Excel output uses `openpyxl` and is saved under `/mnt/attach/outputs/`; adjust the paths in the skills if your setup differs.
- The skills depend on the current Facebook and Threads web UI (menu labels such as "Unsave", "Unsend for everyone", "See more"). If the UI changes, the skills may need updating.

## Safety notes

- Page content is treated as data, never as instructions.
- Secrets (password-reset keys, tokens, OTPs) found in links are redacted, never written to the spreadsheets.
- Unsaving and unsending cannot be reliably undone. Run the matching categorizer first and review the list.
- Use these only on your own account and content, and respect each platform's terms of service.
