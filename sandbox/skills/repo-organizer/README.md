# Repository Organizer Skill

A systematic SPARC skill for maintaining repository organization, tracking provenance, managing metadata, and archiving completed research while keeping active outputs clean and well-documented.

## 🎯 Purpose

After multi-agent research and development workflows, repositories accumulate research files, intermediate outputs, and compiled deliverables. This skill:

- **Organizes** scattered files into logical structures
- **Tracks** provenance chains (which research led to which outputs)
- **Archives** completed pre-cursor research once outputs exist
- **Enhances** files with metadata (sequence, type, relationships)
- **Documents** repository structure with manifests and maps
- **Maintains** clean separation between active work and historical artifacts

## 🏗️ How It Works

### 5-Phase Workflow

**Phase 1: Discovery & Scanning**
- Scans repository structure (user-specified paths or full repo)
- Identifies file clusters (same directory, similar names, timestamps)
- Detects orphaned/unlabelled files
- Maps current organization state
- Identifies parent-child relationships

**Phase 2: Classification & Analysis**
- Determines file types (research, compiled, skill, code, documentation)
- Identifies completed work (research exists + compiled output exists)
- Detects status (draft, active, completed, archived)
- Maps provenance chains (which files led to which outputs)
- Flags files recommended for archival

**Phase 3: Metadata Enhancement**
- Adds YAML frontmatter to markdown files (if missing)
- Generates manifest.json for file clusters
- Tracks sequence numbering
- Records agent types, tokens consumed, timestamps
- Links parent-child relationships

**Phase 4: Archival & Organization**
- Creates archive directories: `archives/YYYY-MM-DD-project-name/`
- Moves (not copies) pre-cursor research files
- Generates archive README explaining contents
- Updates active files to reference archive locations
- Preserves directory structure in archives

**Phase 5: Documentation & Indexing**
- Updates/creates `.repo-map.json` (repository index)
- Generates PROVENANCE.md for major outputs
- Creates directory READMEs where missing
- Produces organization summary report
- Suggests git commit message with detailed provenance

## 📋 Metadata Schema

### File-Level Frontmatter (YAML)

```yaml
---
# Classification
type: research | compiled | skill | code | documentation | config
status: draft | active | completed | archived
project: project-identifier
phase: discovery | strategy | implementation | testing | deployment

# Provenance
created: 2025-10-22T19:15:00Z
modified: 2025-10-22T20:30:00Z
sequence: 1
parent: null
children: [path/to/file1.md, path/to/file2.md]
derived_from: [source/file1.md, source/file2.md]

# Agent Context
agent_type: researcher | coder | reviewer | tester | coordinator
agent_task: "Analyze r/aiagents for pain points and opportunities"
estimated_tokens: 41000

# Descriptive
tags: [research, market-analysis, reddit, ai-agents]
author: Task Agent (researcher)
summary: "Analysis of r/aiagents subreddit sentiment and pain points"
---
```

### Directory-Level Manifest (JSON)

```json
{
  "project": "webinar-funnel-research",
  "created": "2025-10-22T18:00:00Z",
  "status": "archived",
  "total_files": 9,
  "total_size_kb": 461,
  "total_tokens": 410000,
  "agents_used": [
    {"type": "researcher", "count": 5},
    {"type": "analyst", "count": 4}
  ],
  "files": [
    {
      "name": "01-reddit-analysis.md",
      "type": "research",
      "size_kb": 41,
      "sequence": 1,
      "agent": "researcher",
      "summary": "r/aiagents community pain points and language patterns"
    }
  ],
  "outputs_generated": [
    "sandbox/skills/webinar-funnel-builder/README.md",
    "sandbox/skills/webinar-funnel-builder/strategy-template.md"
  ],
  "provenance_note": "Research conducted to build Webinar Funnel Strategist SPARC skill"
}
```

### Repository Map (`.repo-map.json`)

```json
{
  "generated": "2025-10-22T21:00:00Z",
  "version": "1.0.0",
  "projects": {
    "webinar-funnel-strategy": {
      "status": "completed",
      "created": "2025-10-22",
      "description": "Webinar funnel strategy builder skill with market research",
      "active_outputs": [
        ".claude/sparc-modes.json (webinar-funnel mode)",
        "sandbox/skills/webinar-funnel-builder/"
      ],
      "archived_research": "archives/2025-10-22-webinar-funnel-research/",
      "total_tokens_consumed": 461000,
      "agents_used": 9,
      "provenance_chain": ["research → skill definition → templates"]
    }
  },
  "archives": [
    {
      "path": "archives/2025-10-22-webinar-funnel-research/",
      "project": "webinar-funnel-strategy",
      "files": 9,
      "size_kb": 461,
      "archived_date": "2025-10-22"
    }
  ],
  "directory_structure": {
    "sandbox/": "Active development workspace",
    "archives/": "Completed research and historical artifacts",
    ".claude/": "Claude Flow configuration and SPARC modes",
    "docs/": "Project documentation"
  }
}
```

## 🚀 Usage

### Basic Usage

```bash
# Organize specific directory
npx claude-flow sparc run repo-organizer "Organize sandbox/research/ - archive completed work"

# Full repository cleanup
npx claude-flow sparc run repo-organizer "Full repository organization and metadata update"

# Archive specific project
npx claude-flow sparc run repo-organizer "Archive webinar funnel research files, compiled outputs exist in sandbox/skills/"
```

### Example Prompts

**After completing research project:**
```
"Organize the webinar funnel research. The 9 research files in sandbox/research/
should be archived since we've compiled them into sandbox/skills/webinar-funnel-builder/.
Add metadata, create manifests, and generate provenance documentation."
```

**General cleanup:**
```
"Scan entire repository and organize any scattered files. Add metadata where missing,
create directory READMEs, and update repository map."
```

**Specific file cluster:**
```
"Organize all .md files in docs/ - add frontmatter metadata, sequence numbering,
and create manifest.json"
```

## 📊 Expected Outcomes

### Before Organization

```
sandbox/research/
  reddit-analysis.md (41KB, no metadata)
  competitor-analysis.md (53KB, no metadata)
  income-opportunities.md (38KB, no metadata)
  market-sentiment.md (43KB, no metadata)
  audience-segmentation.md (47KB, no metadata)
  webinar-funnel-best-practices.md (67KB, no metadata)
  kartra-platform-strategies.md (48KB, no metadata)
  webinar-ad-strategies.md (68KB, no metadata)
  funnel-psychology-offer-design.md (58KB, no metadata)

sandbox/skills/webinar-funnel-builder/
  README.md (compiled output, no provenance info)
  strategy-template.md
```

### After Organization

```
archives/2025-10-22-webinar-funnel-research/
  _manifest.json (complete index with metadata)
  README.md (explains archive contents and provenance)
  research/
    01-reddit-analysis.md (with YAML frontmatter)
    02-competitor-analysis.md (with YAML frontmatter)
    03-income-opportunities.md (with YAML frontmatter)
    04-market-sentiment.md (with YAML frontmatter)
    05-audience-segmentation.md (with YAML frontmatter)
    06-webinar-funnel-best-practices.md (with YAML frontmatter)
    07-kartra-platform-strategies.md (with YAML frontmatter)
    08-webinar-ad-strategies.md (with YAML frontmatter)
    09-funnel-psychology-offer-design.md (with YAML frontmatter)

sandbox/skills/webinar-funnel-builder/
  README.md (with provenance section: "Built from archives/2025-10-22-webinar-funnel-research/")
  strategy-template.md
  PROVENANCE.md (detailed provenance chain)

.repo-map.json (complete repository index)
```

### Generated Documentation

**Archive README** (`archives/2025-10-22-webinar-funnel-research/README.md`):
```markdown
# Webinar Funnel Research Archive
**Archived:** 2025-10-22
**Project:** webinar-funnel-strategy
**Status:** Completed

## Contents
9 research reports (461KB total) conducted to build the Webinar Funnel Strategist SPARC skill.

## Research Agents Used
- 5 Market Research agents
- 4 Strategy Analysis agents

## Outputs Generated
- `.claude/sparc-modes.json` (webinar-funnel mode)
- `sandbox/skills/webinar-funnel-builder/`

## Files in Archive
[Detailed list with summaries...]
```

## 🎯 When to Use

1. **After completing multi-agent research** - Archive research once compiled outputs exist
2. **Project milestones** - Organize at major checkpoints (MVP, launch, etc.)
3. **Before major refactoring** - Create snapshot of current state
4. **Regular maintenance** - Weekly/monthly cleanup to prevent clutter
5. **Before handing off work** - Ensure clear provenance for collaborators

## ⚙️ Configuration Options

The skill respects these parameters in prompts:

- **Scope:** Specific directories vs. full repository
- **Archive strategy:** Move vs. copy files
- **Metadata depth:** Minimal vs. comprehensive frontmatter
- **Git integration:** Auto-commit vs. suggest commit message
- **Preserve structure:** Flatten vs. maintain directory hierarchy in archives

## 📈 Success Metrics

**Organization Quality:**
- ✅ All markdown files have YAML frontmatter metadata
- ✅ Each archived cluster has manifest.json
- ✅ Repository map exists and is current
- ✅ Provenance chains are documented
- ✅ No orphaned/unlabelled files

**Archive Integrity:**
- ✅ Archive READMEs explain contents
- ✅ Sequence numbering is logical
- ✅ Parent-child relationships are accurate
- ✅ All references are updated (no broken links)

**Execution Time:**
- Small project (< 20 files): 5-10 minutes
- Medium project (20-100 files): 10-20 minutes
- Large repository: 20-40 minutes

## 🔗 Works Well With

- **Webinar Funnel Strategist** - Organize after strategy generation
- **SPARC TDD workflows** - Archive research after refinement phase
- **Multi-agent swarms** - Clean up after parallel agent execution
- **GitHub PR workflows** - Organize before creating pull requests

## 💡 Tips for Best Results

1. **Run after major milestones** - Don't wait until repository is cluttered
2. **Be specific in prompts** - Name the project/directory to organize
3. **Review suggestions first** - Skill will recommend changes before executing
4. **Keep archives in git** - Valuable for provenance and rollback
5. **Update .gitignore** - Exclude runtime files but keep archives
6. **Use consistent naming** - Helps skill detect project boundaries

## ⚠️ Limitations

- Does not organize binary files (images, PDFs) - only text/markdown
- Requires clear project boundaries (scattered unrelated files are harder to cluster)
- Git integration is advisory only (user must commit)
- Archive location is always `archives/` (not configurable per-run)

## 🛠️ Technical Details

**File Detection:**
- Groups files by directory, naming patterns, timestamps (within 24 hours)
- Uses git history to detect relationships (if available)
- Parses imports/links to identify dependencies

**Metadata Inference:**
- Agent type from file content analysis
- Token count from file size estimates
- Sequence from timestamps and content dependencies
- Project name from directory or git branch

**Safety:**
- Never deletes files (moves to archives)
- Creates backups before bulk operations
- Validates archive structure before moving files
- Reports all changes for user review

---

**Built by:** humanrace.ai
**Compatible with:** Claude Flow v2.7.0+
**License:** MIT
**Last Updated:** 2025-10-22
