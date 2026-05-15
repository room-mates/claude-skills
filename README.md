# Claude Skills — Verrlo

Custom slash commands for Claude Code used across Verrlo projects.

## What this is

Each `.md` file in the `commands/` folder is a Claude Code skill — a reusable
prompt that can be invoked with `/skill-name` in any Claude Code session once
synced locally.

## Skills

| Skill | Command | Description |
|-------|---------|-------------|
| Seed mock data | `/seed-mock-data` | Seed the local Docker Postgres database with realistic mock data for all features |

## Syncing locally

Run this once to set up the skills on a new machine:

```bash
# Clone the repo
git clone https://github.com/room-mates/claude-skills.git ~/claude-skills

# Symlink commands into Claude Code's user commands directory
mkdir -p ~/.claude/commands
for f in ~/claude-skills/commands/*.md; do
  ln -sf "$f" ~/.claude/commands/
done
```

After that, pull and re-run the loop whenever new skills are added:

```bash
cd ~/claude-skills && git pull
for f in ~/claude-skills/commands/*.md; do
  ln -sf "$f" ~/.claude/commands/
done
```

## Adding a new skill

1. Create `commands/your-skill-name.md` following the format of existing skills
2. Commit and push
3. Re-run the sync loop above on each machine
