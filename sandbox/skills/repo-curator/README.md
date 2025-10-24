# Repo Curator Skill

**Automatic post-task curation for sandbox/ markdown research with metadata tracking, archival, and auto-commit.**

## 🎯 Purpose

An automatic repository curator triggered by **post-task hooks** that runs after markdown research tasks complete. It organizes `sandbox/` files, adds metadata with task IDs and dates, archives completed research, and auto-commits changes without user intervention.

### Why Automatic?

Multi-agent research workflows produce numerous markdown files. This skill ensures every completed task leaves behind:
- ✅ Properly tagged metadata (task ID, date, provenance)
- ✅ Archived research when compiled outputs exist
- ✅ Clean sandbox/ directory structure
- ✅ Auto-committed changes for complete audit trail
- ✅ Manifests and documentation

**Runs automatically** - no manual invocation needed. Set up the post-task hook once, then forget about it.

## 🔄 How It Works

### Trigger: Post-Task Hook

Automatically runs when tasks complete via:
```bash
npx claude-flow@alpha hooks post-task --task-id "task-12345"
```

The hook calls this skill which then:

### 5-Step Automatic Workflow

**1. DETECT & SCAN (Sandbox Only)**
- Scans `sandbox/` for new/modified `.md` files since last curation
- Identifies research files without YAML frontmatter metadata
- Detects completed research (research files + compiled output exists)
- Groups related files by directory and timestamps
- **Scope:** Only `sandbox/` directory (ignores `.claude/`, root, etc.)

**2. ADD METADATA**
Adds YAML frontmatter to all markdown files in sandbox/:

```yaml
---
task_id: "task-12345"              # From post-task hook context
completion_date: "2025-10-22"      # YYYY-MM-DD format
created: "2025-10-22T19:15:00Z"    # ISO 8601 timestamp
type: research                      # research|compiled|skill|documentation
status: completed                   # completed|archived
project: "webinar-funnel-strategy" # Inferred from directory/content
sequence: 1                         # Numbering within project
agent_type: researcher              # Inferred from content
estimated_tokens: 41000             # Estimated from file size
tags: [research, market-analysis]
summary: "Brief description of file contents"
---
```

**3. ARCHIVE IF COMPLETE**
When research is complete (compiled output exists):
- Creates: `sandbox/archives/YYYY-MM-DD-project-name/`
- Moves research files from `sandbox/research/` to archive
- Generates `_manifest.json` with complete task IDs and dates
- Creates archive README with full provenance chain
- Updates compiled outputs to reference archive location

**4. ORGANIZE ACTIVE FILES**
For active (non-archived) files in sandbox/:
- Ensures proper directory structure
- Adds sequence numbering (01-, 02-, 03- prefixes)
- Generates directory `_manifest.json` if 3+ files present

**5. AUTO-COMMIT**
Automatically commits changes with detailed message:

```
Curate sandbox: webinar-funnel-strategy research - Task task-12345

- Added metadata to 9 markdown files
- Archived 9 completed research files to sandbox/archives/2025-10-22-webinar-funnel/
- Generated manifests and provenance documentation

Task ID: task-12345
Completion Date: 2025-10-22
Files processed:
  - sandbox/research/reddit-analysis.md
  - sandbox/research/competitor-analysis.md
  - ... (7 more files)
```

## 📋 Required Metadata Fields

Every markdown file gets these **REQUIRED** fields:

| Field | Description | Example |
|-------|-------------|---------|
| `task_id` | Unique task identifier from hook | `"task-12345"` |
| `completion_date` | Date task completed (YYYY-MM-DD) | `"2025-10-22"` |
| `created` | ISO 8601 timestamp | `"2025-10-22T19:15:00Z"` |
| `type` | File classification | `research` \| `compiled` \| `skill` |
| `status` | Lifecycle status | `completed` \| `archived` |
| `project` | Project identifier | `"webinar-funnel-strategy"` |

**Optional but recommended:**
- `sequence` - Numbering within project (1, 2, 3...)
- `agent_type` - Agent that created file (researcher, coder, analyst)
- `estimated_tokens` - Token count for cost tracking
- `tags` - Searchable keywords
- `summary` - Brief description

## 🗂️ Archive Structure

When research is archived, the structure is:

```
sandbox/
├── research/               # Active research (gets archived when complete)
│   └── (empty after archival)
├── skills/                 # Compiled outputs (stay here permanently)
│   └── webinar-funnel-builder/
│       ├── README.md
│       └── strategy-template.md
└── archives/               # Completed research archives
    └── 2025-10-22-webinar-funnel-research/
        ├── _manifest.json                  # Complete metadata index
        ├── README.md                       # Provenance documentation
        └── research/
            ├── 01-reddit-analysis.md       # Archived with metadata
            ├── 02-competitor-analysis.md
            └── ... (all research files)
```

### Archive Manifest Example

`sandbox/archives/2025-10-22-webinar-funnel-research/_manifest.json`:

```json
{
  "project": "webinar-funnel-research",
  "task_id": "task-12345",
  "completion_date": "2025-10-22",
  "created": "2025-10-22T20:15:00Z",
  "status": "archived",
  "total_files": 9,
  "total_tokens": 410000,
  "files": [
    {
      "name": "01-reddit-analysis.md",
      "task_id": "task-12345",
      "completion_date": "2025-10-22",
      "sequence": 1,
      "agent": "researcher",
      "estimated_tokens": 41000
    }
  ],
  "outputs_generated": [
    "sandbox/skills/webinar-funnel-builder/README.md"
  ]
}
```

## 🔗 Hook Integration

### Setup (One-Time)

Configure claude-flow to trigger this skill after task completion:

```bash
# Edit .claude/hooks.json or hooks configuration
{
  "post-task": {
    "command": "npx claude-flow@alpha sparc run repo-curator",
    "env": {
      "TASK_ID": "$TASK_ID",
      "TASK_DATE": "$TASK_DATE",
      "WORKING_DIR": "sandbox/"
    }
  }
}
```

### Hook Behavior

When a task completes:
1. Post-task hook fires with task ID and date
2. Repo Curator skill runs automatically
3. Scans `sandbox/` for new/modified markdown files
4. Adds metadata, archives if needed
5. Auto-commits changes with detailed message
6. Completes in under 30 seconds
7. User sees clean commit in git history

**No manual intervention required.**

## ⚡ Execution Speed

- **Small tasks** (1-5 files): 5-10 seconds
- **Medium tasks** (5-15 files): 10-20 seconds
- **Large tasks** (15+ files): 20-30 seconds

Runs silently in background after task completion.

## 🎯 When It Runs

Automatically triggered when:

✅ **Markdown research task completes** - Most common
✅ **Multi-agent swarm finishes** - Curates all agent outputs
✅ **SPARC mode completes** - Organizes generated files
✅ **Skill execution ends** - Archives research

Does NOT run for:
❌ Code-only tasks (no markdown files)
❌ Tasks outside `sandbox/`
❌ Manual file edits (unless task completes)

## 🛡️ Safety Features

- **Never deletes files** - Only moves to archives
- **Preserves git history** - All changes committed
- **Validates before moving** - Checks archive structure
- **Skips files with metadata** - Won't overwrite existing frontmatter
- **Sandbox-only scope** - Won't touch `.claude/`, root files, etc.
- **Idempotent** - Safe to run multiple times

## 📊 Example Workflow

### Before Curation

```
sandbox/
└── research/
    ├── reddit-analysis.md (no metadata)
    ├── competitor-analysis.md (no metadata)
    ├── income-opportunities.md (no metadata)
    └── ... (6 more files, no metadata)
```

### After Automatic Curation (Post-Task Hook)

```
sandbox/
├── archives/
│   └── 2025-10-22-webinar-funnel-research/
│       ├── _manifest.json
│       ├── README.md
│       └── research/
│           ├── 01-reddit-analysis.md (✅ metadata added)
│           ├── 02-competitor-analysis.md (✅ metadata added)
│           └── ... (all files with metadata)
├── research/
│   └── (empty - archived)
└── skills/
    └── webinar-funnel-builder/
        └── README.md (✅ references archive location)
```

### Git Commit Created

```
commit abc123
Author: Claude <noreply@anthropic.com>
Date: 2025-10-22

Curate sandbox: webinar-funnel-research - Task task-12345

- Added metadata to 9 markdown files
- Archived 9 completed research files to sandbox/archives/2025-10-22-webinar-funnel/
- Generated manifests and provenance documentation

Task ID: task-12345
Completion Date: 2025-10-22
Files processed: [list of 9 files]
```

## 🔍 Metadata Template

See `templates/metadata-schema.yaml` for full YAML frontmatter template.

**Minimal Required:**
```yaml
---
task_id: "task-12345"
completion_date: "2025-10-22"
created: "2025-10-22T19:15:00Z"
type: research
status: completed
project: "project-name"
---
```

**Complete Example:**
```yaml
---
task_id: "task-webinar-001"
completion_date: "2025-10-22"
created: "2025-10-22T19:15:00Z"
modified: "2025-10-22T20:30:00Z"
type: research
status: archived
project: "webinar-funnel-strategy"
phase: discovery
sequence: 1
parent: null
children: ["sandbox/skills/webinar-funnel-builder/README.md"]
derived_from: null
agent_type: researcher
agent_task: "Analyze r/aiagents subreddit for pain points"
estimated_tokens: 41000
tags: [research, reddit, market-analysis, ai-agents]
author: "Task Agent (researcher)"
summary: "Analysis of r/aiagents community sentiment and pain points"
---
```

## 💡 Benefits

### For Users
- **Zero manual work** - Runs automatically after tasks
- **Complete audit trail** - Every file tracked with task ID and date
- **Clean workspace** - Completed research archived, active work visible
- **Git history** - Full provenance in commit messages

### For Teams
- **Discoverability** - Find research by task ID, date, or project
- **Accountability** - Know which agent created what, when
- **Cost tracking** - Token estimates per task
- **Knowledge retention** - Archives preserve context

### For AI Agents
- **Context loading** - Read manifests to understand project history
- **Provenance chains** - Follow parent-child relationships
- **Avoid duplication** - Check archives before re-researching

## 🔧 Configuration

### Metadata Inference

The skill automatically infers:
- **Project name**: From directory name or file content
- **Agent type**: From file analysis (mentions "research", "analysis", "code")
- **Sequence**: From file timestamps and dependencies
- **Tags**: From content analysis (keywords, topics)
- **Summary**: From first paragraph or heading

### Archive Triggers

Archives research when:
1. Research files exist in `sandbox/research/`
2. Compiled output exists in `sandbox/skills/` or `sandbox/`
3. Task status is "completed"

If research is incomplete, adds metadata but does NOT archive.

## 📝 Templates

### Included Templates

1. **metadata-schema.yaml** - Complete YAML frontmatter reference
2. **manifest-template.json** - Directory manifest structure
3. **archive-readme-template.md** - Archive documentation template

All templates are in `sandbox/skills/repo-curator/templates/`

## 🚀 Quick Start

### 1. Enable Post-Task Hook

Add to your `.claude/hooks.json`:
```json
{
  "post-task": "npx claude-flow@alpha sparc run repo-curator"
}
```

### 2. Run Tasks as Normal

```bash
# Run any task that generates markdown in sandbox/
npx claude-flow sparc run webinar-funnel "Build strategy for AI agents"
```

### 3. Curation Happens Automatically

When task completes:
- ✅ Metadata added to all `.md` files in `sandbox/`
- ✅ Completed research archived to `sandbox/archives/`
- ✅ Changes auto-committed to git
- ✅ Summary logged

### 4. Check Results

```bash
# View archive
ls sandbox/archives/

# Check git log
git log -1

# Read manifest
cat sandbox/archives/2025-10-22-project-name/_manifest.json
```

## ⚠️ Important Notes

- **Sandbox-only**: Only processes `sandbox/` directory
- **Automatic**: No manual invocation needed with hook
- **Auto-commit**: Commits directly to current branch
- **Metadata-aware**: Won't overwrite existing frontmatter
- **Idempotent**: Safe to run multiple times on same files

## 🤝 Works Great With

- **Webinar Funnel Strategist** - Auto-curates after strategy generation
- **Multi-agent swarms** - Organizes all agent outputs automatically
- **SPARC workflows** - Archives research after refinement phase
- **Any markdown-generating task** - Universal curation

## 📊 Success Metrics

After running, you should see:
- ✅ All markdown files in `sandbox/` have YAML frontmatter
- ✅ Completed research moved to dated archives
- ✅ Manifests generated with task IDs and dates
- ✅ Git commit with detailed provenance message
- ✅ Clean `sandbox/` structure

---

**Built by:** humanrace.ai
**Compatible with:** Claude Flow v2.7.0+
**Hook:** Post-task
**Scope:** sandbox/ only
**Mode:** Automatic
**Last Updated:** 2025-10-22
