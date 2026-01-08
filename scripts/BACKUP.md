# Backup Script

Complete backup solution for Clawdbot including all workspaces, agents, and sandboxes.

## Usage

```bash
./scripts/backup-complete.sh
```

## What Gets Backed Up

The script creates a comprehensive backup in `~/.backup/clawdbot/<timestamp>/`:

### Core State (120M)
- `~/.clawdbot/` - Complete state directory including:
  - Agent configurations
  - Session history (all agents)
  - Sandbox workspaces (6 sandboxes)
  - Credentials
  - Logs
  - Cron jobs
  - Browser state

### Workspaces
All configured agent workspaces are backed up with their original directory names:
- `clawd/` - Main agent workspace
- `clawd-family/` - Family agent workspace
- `clawd-cyberheld-alfred/` - Alfred agent workspace

The script automatically discovers workspaces by parsing `routing.agents.*.workspace` in your config.

### Docker Volumes (optional)
If Docker is running, the script will also export all `clawdbot-*` Docker volumes as `.tar.gz` files.

## Backup Structure

```
~/.backup/clawdbot/20260108125916/
├── .clawdbot/                    # Complete state (120M)
├── clawd/                        # Main workspace (3.1M)
├── clawd-family/                 # Family workspace (28K)
├── clawd-cyberheld-alfred/       # Alfred workspace (2.3M)
└── docker-volumes/               # Docker volumes (if available)
    └── clawdbot-sandbox.tar.gz
```

## Restore

The script provides restore commands at the end of the backup. Example:

```bash
# Restore everything
rsync -a ~/.backup/clawdbot/<timestamp>/.clawdbot/ ~/.clawdbot/

# Restore specific workspace
rsync -a ~/.backup/clawdbot/<timestamp>/clawd/ ~/clawd/
```

## Features

- ✅ **Complete**: Backs up all state, workspaces, and sandboxes
- ✅ **Smart**: Auto-discovers workspaces from config
- ✅ **Safe**: Uses rsync for reliable copying
- ✅ **Timestamped**: Each backup has unique timestamp
- ✅ **Summary**: Shows what was backed up with sizes
- ✅ **Restore hints**: Provides ready-to-use restore commands

## Output Example

```
📦 Creating complete Clawdbot backup...
Timestamp: 20260108125916
Target: /Users/pascal/.backup/clawdbot/20260108125916

=== Core State Directory ===
📁 Backing up: /Users/pascal/.clawdbot
  ✅ 120M - .clawdbot (complete)

=== Workspace Directories ===
📁 clawd
   Source: /Users/pascal/clawd
   ✅ 3,1M (114 files)
📁 clawd-family
   Source: /Users/pascal/clawd-family
   ✅  28K (7 files)
📁 clawd-cyberheld-alfred
   Source: /Users/pascal/clawd-cyberheld-alfred
   ✅ 2,3M (24 files)

=== Agent Summary ===
🤖 alfred: 2 sessions → /Users/pascal/clawd-cyberheld-alfred
🤖 family: 2 sessions → /Users/pascal/clawd-family
🤖 main: 47 sessions → /Users/pascal/clawd

=== Sandbox Summary ===
🐳 6 sandbox workspace(s) (included in .clawdbot backup)

✅ Backup complete!

📊 Backup Structure:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  clawd/                                  3,1M (114 files)
  clawd-cyberheld-alfred/                 2,3M (24 files)
  clawd-family/                            28K (7 files)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Total: 125M
```

## When to Use

- Before major updates or configuration changes
- Before testing new features
- Regular maintenance backups
- Before migrating to a new machine
- After important agent sessions

## Requirements

- `rsync` (pre-installed on macOS/Linux)
- `jq` (for parsing config)
- Optional: Docker (for volume backups)
