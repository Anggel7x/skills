---
name: clean-docs
description: Sweep a repo's markdown — delete every doc the code contradicts or already says, and cut what survives down to size.
disable-model-invocation: true
---

# clean-docs

The code is the only witness. A doc earns its place by saying something the code cannot say, in lines the code confirms. A doc that has drifted is worse than no doc, because the next agent believes it.

Delete beats rewrite. A rewritten doc is one you must keep true forever; a deleted one is free. Rewrite only the four kinds under **What survives** — everything else is a delete.

## Steps

1. **Inventory.** Get the tree clean (`git status`) first: the sweep then lands as one reviewable diff, and every tracked deletion is recoverable from history. The invocation may name a subtree; absent one, the scope is the repo root. List what the repo owns — `git ls-files '*.md' '*.mdx'` — then add untracked docs (`git status --porcelain -uall`) and mark them, since deleting those is unrecoverable. Vendored trees (`node_modules`, `Pods`, `.venv`, `site-packages`) are someone else's docs. Tool-regenerated output (graph reports, API dumps, coverage) is build product — it belongs in `.gitignore`, not in the sweep. Done when every markdown path in scope is either in the inventory or excluded for a named reason.

2. **Judge each doc against the code.** Read the doc, then go to the code and confirm each claim it makes — paths, commands, endpoints, flags, schemas, behaviour. Grep for what it names; run the command it prints. A claim you cannot land on a file, a line, or a command's output has drifted. Then classify against **What survives** and **What goes** below. Done when every doc in the inventory carries a verdict — delete, or cut to N lines — and every line you propose to keep names the code that confirms it.

3. **Show the ledger.** One table: path, current lines, verdict, reason in a few words, untracked deletions flagged. Anything a doomed doc still holds live — an open task, an undecided question — goes to wherever the project tracks its state (Notion, GitHub issues, or elsewhere) before its file goes. Done when the user has approved the table.

4. **Cut, then chase the references.** Apply the deletions and rewrites. Then grep the whole repo — source, CI configs, package scripts, surviving docs — for the basename of every file removed. A README link, an mkdocs nav entry, or a CI step reading a deleted path is breakage you caused. Done when that grep comes back empty and `git diff --stat` matches the ledger.

## What survives

Four kinds, each cut back to the lines the code confirms:

- **README** — what this is, how to run it, how to test it.
- **Agent instructions** — `CLAUDE.md`, `.claude/rules/`, `AGENTS.md`: the conventions the agent must follow.
- **Operational facts the tree can't hold** — deploy hosts, where secrets live, external accounts, run-books for systems outside the repo.
- **Why a non-obvious decision was made** — and only when the reason spans files. A reason that fits beside one function is a code comment: move it there, then delete the doc.

Cap: 200 lines. Over it, cut — splitting one doc into two multiplies what you must keep true.

## What goes

- **Specs, plans, designs, roadmaps, TODOs, audits, reports, status write-ups, post-mortems.** These are state, and state lives in the project's tracker (Notion, GitHub issues, or elsewhere); the repo can only hold a stale copy of it.
- **Anything the code contradicts.** The code is right by definition, so the doc is drift.
- **Anything the code already says.** A doc that walks a module's structure, lists its files, or restates its signatures is a second copy of the code that decays on the next commit.
- **Docs about finished work.** The diff is the record.
