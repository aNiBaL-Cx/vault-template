# Vault structure

This file is the vault's map and its placement rules. People and agents read it before creating a note; the `notes` skill resolves every folder from it.

## Structure

```
<Vault>/
├── 00_Daily/          One note per day (YYYY-MM-DD-Weekday.md)
├── 10_Projects/       Active work, one subfolder per project
│   └── <Project>/
│       ├── Tasks/     One folder per task: <ID>/<ID>-index.md
│       └── ...        Project notes that are not tasks
├── 20_Areas/          Ongoing responsibilities with no end date
├── 30_Resources/      Reference material by topic, never by project
│   └── Keys/          Credential references; ignored by git
├── 40_Archive/        Finished work, same layout as 10_Projects/
├── 90_Inbox/          Not yet filed; emptied weekly
├── Templates/         Templater templates
├── Dashboard.md       Daily hub: active projects, quick links
├── AGENTS.md          Instructions for AI agents
├── CLAUDE.md          One line, `@AGENTS.md`, so Claude Code reads the same file
└── README.md          This file
```

The number prefixes only set the sort order in the file explorer.

## Placement rules

- **A task** (has an ID in a tracker, or is a unit of work with an end) → `10_Projects/<Project>/Tasks/<ID>/<ID>-index.md`. Sub-notes are `<ID>-<what>.md` in the same folder.
- **Project notes that are not a task** (plans, specs, meeting notes for that project) → `10_Projects/<Project>/`.
- **Something you keep looking up** (how-to, architecture, research) → `30_Resources/<Topic>/`. If it is only true for one project, it still goes here, named after the topic.
- **An ongoing responsibility** with no end → `20_Areas/<Area>/`.
- **Today's notes, quick thoughts, ad-hoc meetings** → the daily note.
- **Unsure** → `90_Inbox/`, filed within a week.
- **Finished** → move to `40_Archive/` in Obsidian, keeping the same path below the top folder, so links update. To group a large archive (for example by year, `40_Archive/<Project>/Tasks/<YYYY>/<ID>/`), state the rule here and agents follow it.

## Naming

- Task IDs come from the project's tracker. With no tracker, use `TASK-001`, `TASK-002`… per project.
- Every note in a task folder starts with the ID, so the filename alone identifies it.
- Other notes: `Title-In-Words.md`. No dates in names except daily notes.

## Using this template

1. Copy it: `git clone <template-url> <Vault> && rm -rf <Vault>/.git`, or "Use this template" on GitHub.
2. Open the folder as a vault in Obsidian and install the community plugins listed in `.obsidian/community-plugins.json` (Settings → Community plugins → Browse). Their settings are already in place.
3. Edit `AGENTS.md`: vault name, purpose, tracker.
4. Agent skills (`notes` and others): run `install.sh` from the [aNiBaL-Cx/dotfiles](https://github.com/aNiBaL-Cx/dotfiles) repo. It links each skill in `agents/skills/` into `~/.claude/skills/` (Claude Code) and `~/.agents/skills/` (Codex, Copilot, Gemini). The same script replaces `~/.zshrc`, `~/.tmux.conf`, `~/.config/nvim`, `~/.claude/CLAUDE.md` and more with links into that repo, so read it first; to get only the skills, link the `agents/skills/<name>` folders yourself.
   - Obsidian syntax skills for agents (wikilinks, callouts, properties, Bases, Canvas) come from [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills): `npx skills add https://github.com/kepano/obsidian-skills -g -y -s obsidian-markdown -s obsidian-bases -s json-canvas`.
5. Optional, git history:
   - `git init -b main`, first commit, private remote.
   - Secrets hook and daily commit (macOS, needs `gitleaks`): run `vault-git/install.sh <name> <Vault> [HH:MM]` from the [aNiBaL-Cx/dotfiles](https://github.com/aNiBaL-Cx/dotfiles) repo. It links the gitleaks pre-commit hook and schedules a daily commit and push (default 19:30), logging to `~/Library/Logs/vault-commit-<name>.log`. The hook and the agent point into that checkout, so keep it where it is; clone it first if you have none (`git clone https://github.com/aNiBaL-Cx/dotfiles`).
6. Optional, project link: in each project repo that works with this vault, `ln -s <Vault> notes` at the repo root, and add `notes` to that repo's `.git/info/exclude`.

**Sync** is your choice: git only, or a sync service on top. If the service syncs dot-folders, exclude `.git/` from it.
