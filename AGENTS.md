# <Vault name>

<One line: what this vault is for and whose it is.>

It is both a human reference and working context for AI agents. Read `README.md` for the folder map and placement rules before creating any note.

## Start here

- `Dashboard.md`: active projects and quick links.
- `README.md`: structure, placement, naming.

## Conventions

- **Who owns what:** the tracker owns status, acceptance criteria and discussion; the code host owns the diff; notes own the reasoning, the dead ends and the links. If the tracker or the code host can hold it, link it.
- **Tasks:** one folder per task, `10_Projects/<Project>/Tasks/<ID>/`. The main note is `<ID>-index.md`; every other file in the folder starts with the ID.
- **Daily notes:** today's events and next actions only. What a task is lives in its index, one link away.
- **Writing:** lead with the problem in plain words, one idea per sentence, no hard-wrapped prose.

## Notes skill: this vault's specifics

- **Tracker:** <none | name>. Task IDs look like <`TASK-001`>.
- **Templates:** task index `Templates/Task Index.md`, investigation `Templates/Investigation.md`, daily note `Templates/Daily Note.md`.
- **Close-out:** none beyond the skill's archive steps.

## Sensitive files

- **Never paste a credential into a note.** Reference `30_Resources/Keys/<file>` or a password-manager item instead. `30_Resources/Keys/` is ignored by git.
- In `curl`, env and log snippets, replace every credential (API keys, tokens, cookies, session ids, passwords, `Authorization` and custom auth headers) with `$VAR` or `<redacted>`.
- When the vault is a git repo with the pre-commit hook, `gitleaks` blocks a commit that holds a secret. A deliberate false positive gets `<!-- gitleaks:allow -->` at the end of its line.
