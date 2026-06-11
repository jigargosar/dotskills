# Skill dev workflow

Backups in docs/backup/ are frozen snapshots — never edit them.
Keep backups unique — snapshot only when the skill changed.

## Edit original skill in place
1. Iterate: edit, back up, restart the session, test.
   backup → docs/backup/<original-name>-SKILL-v<N>.md

## Edit a copy of original skill
1. Decide the copy's name (e.g. flo → flo-v2).
2. Copy the original skill folder to a sibling with that name, and set the name: field inside SKILL.md to match.
3. Iterate: edit, back up, restart the session, test.
   backup → docs/backup/<copy-name>-SKILL-v<N>.md
4. Promote: back up both skills, overwrite the original with the copy while keeping the original's name, then delete the copy folder.
