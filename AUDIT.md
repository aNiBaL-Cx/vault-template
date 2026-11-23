# Vault alignment audit

A prompt for any AI agent: check that this vault still matches the template it was created from, and report what differs. Run it by telling the agent "Read `AUDIT.md` and run it."

The agent needs read access to the vault, a shell with `git`, and network access to GitHub. It edits nothing.

## 0. Use the latest version of this file

This copy is from the day the vault was created, so it may be stale. Fetch the reference first:

```bash
ref=$(mktemp -d)
git clone --depth 1 https://github.com/aNiBaL-Cx/vault-template "$ref/vault-template"
git clone --depth 1 https://github.com/aNiBaL-Cx/dotfiles "$ref/dotfiles"
diff -q AUDIT.md "$ref/vault-template/AUDIT.md"
```

If the two `AUDIT.md` files differ, stop reading this one and run `$ref/vault-template/AUDIT.md` instead. If GitHub is unreachable, run this copy and say so in the report.

Record `git -C "$ref/<repo>" log -1 --format='%h %cs'` for both repos.

## What the reference is

- **`vault-template`:** folder roles, root files (`README.md`, `AGENTS.md`, `CLAUDE.md`, `Dashboard.md`), folder `README.md`s, `Templates/`, `.gitignore`, `.obsidian/` config.
- **`dotfiles`:** the agent skills in `agents/skills/`. The `notes` skill reads the vault, so `agents/skills/notes/SKILL.md` defines which folder roles and index sections the vault must provide. `vault-git/` holds the optional gitleaks hook and daily-commit job.

## What "aligned" means

The template is a contract, not a picture. Folder names may differ (`Projects/` instead of `10_Projects/`) and a vault may add structure, as long as its own `README.md` declares it. Agents resolve every folder from that README, never from the template's names.

Classify each divergence:

- **Breaking:** an agent would place, find or archive a note wrongly; a template or plugin points at a folder that does not exist; or a secret could reach git.
- **Declared:** differs from the template, but the vault's `README.md` or `AGENTS.md` states the rule and the files follow it.
- **Drift:** differs from the template and nothing in the vault declares it, or the vault contradicts its own README.
- **Not applicable:** the check covers an optional part the vault does not use. Say which.

## Read first

1. Reference: everything listed under "What the reference is".
2. The vault: `README.md`, `AGENTS.md`, `CLAUDE.md`, `Dashboard.md`, `.gitignore`, `.obsidian/` config.

## Checks

### 1. Structure

- Map each template folder role (daily, projects, areas, resources, archive, inbox, templates) to one vault folder through the vault README. A role with no folder, or two, is a finding.
- Vault top-level folders the template lacks: each must be declared in the vault README.
- The README's structure tree matches the folders on disk, to depth 3, hidden folders excluded. List each side's extras.
- Every folder role the `notes` skill expects resolves to one existing folder.
- Each role folder has a `README.md` saying what goes in it. Missing is drift.

### 2. Root files

- `CLAUDE.md` is exactly one line, `@AGENTS.md`.
- `AGENTS.md` has the template's sections: Start here, Conventions, "Notes skill: this vault's specifics" (Tracker, Templates, Close-out), Sensitive files.
- No template placeholder is left unfilled (`<Vault name>`, `<none | name>`, `<One line: ...>`).
- Every path named in `AGENTS.md` and `Dashboard.md` exists.

### 3. Placement and naming

Sample; do not read every note. State each sample size.

- **Tasks:** 10 random task folders. Each is `<ID>/<ID>-index.md`, and every file in it starts with `<ID>`.
- **Daily notes:** named `YYYY-MM-DD-Weekday.md`, in the location or grouping the vault README declares.
- **Other notes:** count names that carry a date or are not `Title-In-Words.md`. Report the count and 5 examples.
- **Archive:** mirrors the projects layout below its top folder, or follows the grouping the README declares.
- **Inbox:** list files not modified in 7 days.

### 4. Templates

- The task index, investigation and daily-note templates exist where `AGENTS.md` says. One line per difference from the reference version; no pasted diffs.
- The task index template has the default sections the `notes` skill lists. A missing one is drift.
- The Templater plugin's templates folder and the Daily notes plugin's folder and format point at real folders.

### 5. Obsidian config

- Every plugin in the reference `community-plugins.json` is enabled in the vault and present under `.obsidian/plugins/`. List extras without judging them.
- `.obsidian/*.json` and `.obsidian/plugins/*/data.json`: list keys whose values differ from the reference.

### 6. Secrets and git

Not applicable if the vault is not a git repo; say so and skip.

- `.gitignore` covers every reference entry, mapped to the vault's folder names (the credential folder, `.obsidian/workspace*.json`, `test_results/`).
- The credential folder is ignored: `git check-ignore -v <credential-folder>/x.md`.
- `git ls-files` lists nothing that looks like a credential file (`key`, `secret`, `token`, `password`, `.env`). Report paths only; never print contents.
- The pre-commit hook exists and runs `gitleaks`, and `gitleaks` is installed. If the vault uses `dotfiles/vault-git`, the hook is a link into an existing checkout.
- If a scheduled commit job exists for this vault (`vault-commit.<name>`), it is loaded, its script and vault paths exist, and its log shows a recent run. Report the last 5 log lines.
- If the vault sits in a sync service folder, `.git/` is excluded from the sync. Report only what config files prove; otherwise "not verified".

### 7. Environment (optional)

Check the machine, not the vault. Run it only on the machine where the vault is used.

- The skills in `dotfiles/agents/skills/` are installed where this agent loads skills from. Name that location.
- The installed `notes/SKILL.md` matches the reference. A difference means a stale or locally edited skill.
- If the skills are links into a local dotfiles checkout, report how many commits it is behind `origin/main`.
- Obsidian syntax skills from `kepano/obsidian-skills` are installed: `obsidian-markdown`, `obsidian-bases`, `json-canvas`.

## Rules

- **Read-only.** No edits, moves, renames, commits, plugin installs, scheduler changes or install scripts. The temp clone is the only thing you write.
- **Never open or quote a file in the credential folder.**
- **Evidence for every finding:** a path, plus the command output, line or count that shows it. No finding from assumption.
- **Portable commands.** Use `git`, `find`, `diff`, `ls`, `readlink`. Where an OS differs (macOS `launchctl` against Linux `systemctl`), check the equivalent and name the command used.
- **Declared is not drift**, even when you would design it differently.
- Delete the temp clone at the end.

## Output

Return the report in the conversation. Write it to the vault's inbox as `Vault-Alignment-<YYYY-MM-DD>.md` only if asked.

```
# Vault alignment: <vault name>
vault-template @ <sha> · dotfiles @ <sha> · AUDIT.md: latest | local copy (GitHub unreachable)

**Verdict:** aligned | aligned with declared differences | drift | breaking

## Breaking
- <check> <path>: <what>. Evidence: <...>. Fix: <one line>

## Drift
- ...

## Declared differences
- <template rule> → <vault rule> (declared in <file>)

## Not applicable
- <check>: <why>

## Suggested actions
1. <ordered by risk, one line each>
```

One line per finding. Keep the report under 80 lines.
