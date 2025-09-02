# Usage Guide - Where Documents Land & How to Use the System

## 📁 Where Scaffolded Documents Are Created

### Organized Project Structure
When you run scaffolding commands from the scaffolding repository, documents are created in a `projects/[project-name]/` folder:

```bash
# You're in the scaffolding repo
cd ~/Projekter/scaffolding
/scaffold-all "my-app" "web"

# Creates:
~/Projekter/scaffolding/
├── guides/           # Your guide library (unchanged)
├── .claude/          # Your agents & commands (unchanged)
├── projects/         # All your scaffolded projects
│   └── my-app/       # Your new project
│       ├── docs/
│       │   ├── PRD.md
│       │   ├── ARCHITECTURE.md
│       │   ├── RULES.md
│       │   ├── TASKS.md
│       │   ├── BEST-PRACTICES.md
│       │   ├── PATTERNS.md
│       │   ├── ANTI-PATTERNS.md
│       │   ├── CLAUDE.md
│       │   ├── GIT-STRATEGY.md
│       │   └── EVALS_AND_TESTING.md
│       ├── .claude/
│       │   ├── agents/
│       │   └── commands/
│       └── README.md
```

## 🎯 Recommended Workflow

### Option 1: Create New Project
```bash
# 1. Stay in scaffolding repo
cd ~/Projekter/scaffolding

# 2. Create new project with scaffolding
/scaffold-all "my-awesome-app" "web"

# 3. Your project is created in projects/my-awesome-app/
ls projects/my-awesome-app/
# Shows: docs/ .claude/ README.md

# 4. Move to your development workspace when ready
cp -r projects/my-awesome-app ~/Projects/
cd ~/Projects/my-awesome-app
```

### Option 2: Add Documents to Existing Project
```bash
# 1. From scaffolding repo, create project folder
cd ~/Projekter/scaffolding
/scaffold-prd "existing-app" "web"

# 2. Copy specific documents to your existing project
cp projects/existing-app/docs/PRD.md ~/Projects/existing-app/docs/
```

### Option 3: Manage Multiple Projects
```bash
# Stay in scaffolding repo for all projects
cd ~/Projekter/scaffolding

# Create multiple projects
/scaffold-all "frontend-app" "react"
/scaffold-all "backend-api" "node"
/scaffold-all "mobile-app" "react-native"

# All organized in projects/
ls projects/
# frontend-app/  backend-api/  mobile-app/
```

## 📂 Directory Structure Created

### For Code Projects
```
your-project/
├── docs/                      # All documentation
│   ├── PRD.md                # Product requirements
│   ├── ARCHITECTURE.md       # System design
│   ├── RULES.md              # Non-negotiable constraints
│   ├── TASKS.md              # Work breakdown
│   ├── BEST-PRACTICES.md    # Coding standards
│   ├── PATTERNS.md           # Approved patterns
│   ├── ANTI-PATTERNS.md      # What to avoid
│   ├── CLAUDE.md             # AI agent contract
│   ├── GIT-STRATEGY.md       # Version control
│   └── EVALS_AND_TESTING.md  # Testing strategy
├── .claude/
│   ├── agents/               # Project-specific agents
│   └── commands/             # Project-specific commands
├── src/                      # Your source code
├── tests/                    # Your tests
└── README.md                 # Project overview
```

### For Writing Projects
```
your-book/
├── docs/
│   ├── PROJECT-BRIEF.md     # Concept and audience
│   ├── OUTLINE.md           # Chapter structure
│   ├── STYLE-GUIDE.md       # Writing standards
│   ├── RESEARCH.md          # Research organization
│   ├── CHARACTER-BIBLE.md   # Character development (fiction)
│   └── PRODUCTION-PLAN.md   # Writing schedule
├── chapters/                 # Actual content
├── research/                 # Research materials
└── README.md
```

### For Research Projects
```
your-research/
├── docs/
│   ├── RESEARCH-PROPOSAL.md  # Research plan
│   ├── METHODOLOGY.md        # Research methods
│   ├── DATA-MANAGEMENT.md    # Data handling
│   ├── ANALYSIS-PLAN.md      # Analysis approach
│   ├── ETHICS.md             # Ethics protocol
│   └── DISSEMINATION.md      # Publication strategy
├── data/                      # Research data
├── analysis/                  # Analysis scripts
└── README.md
```

## 🚀 Quick Start Commands

### Full Project Scaffolding
```bash
# Create everything at once
/scaffold-all "ProjectName" "type"

# Types: web, api, cli, library, mobile, desktop
# Also: book, documentation, research, business
```

### Individual Document Scaffolding
```bash
# Create specific documents as needed
/scaffold-prd "web"                    # Create PRD
/scaffold-architecture "react node"    # Create Architecture
/scaffold-tasks                        # Create Tasks from PRD
/scaffold-practices "typescript"       # Create Best Practices
```

### Analysis Commands
```bash
# Run these from your project root
/analyze-parallelism     # Find parallel work opportunities
/detect-antipatterns     # Scan existing code
/validate-docs          # Check document consistency
```

## 💡 Pro Tips

### 1. Use Project Templates
Create a template directory with your preferred structure:
```bash
# Create template
mkdir ~/Templates/web-project
cd ~/Templates/web-project
/scaffold-all "Template" "web"

# Use for new projects
cp -r ~/Templates/web-project ~/Projects/new-project
cd ~/Projects/new-project
# Customize the templates
```

### 2. Version Control Integration
```bash
# After scaffolding
git init
git add docs/
git commit -m "Add project foundation documents"
```

### 3. Team Sharing
```bash
# Share scaffolding with team
cd team-project/
/scaffold-all "TeamProject" "web"
git add .
git commit -m "Add project scaffolding"
git push

# Team members pull and have same foundation
```

### 4. Workspace Organization
```
~/Projects/
├── project-a/
│   └── docs/         # Scaffolded docs
├── project-b/
│   └── docs/         # Scaffolded docs
└── scaffolding/      # This repo (templates/guides)
```

## 🔄 Updating Existing Documents

The scaffolding commands are smart about existing documents:

1. **New files**: Created fresh
2. **Existing files**: You'll be prompted to:
   - Skip (keep existing)
   - Overwrite (replace completely)
   - Merge (append new content)

## 🎨 Customization

### Project-Specific Agents
After scaffolding, create custom agents in `.claude/agents/`:
```bash
cd your-project/.claude/agents/
# Create custom agent for your specific needs
```

### Project-Specific Commands
Add custom commands in `.claude/commands/`:
```bash
cd your-project/.claude/commands/
# Create custom slash commands
```

## 🗺️ Navigation

Once documents are created:
```bash
# View all documentation
ls docs/

# Open specific document
code docs/PRD.md           # VS Code
vim docs/ARCHITECTURE.md   # Vim
cat docs/TASKS.md          # Terminal

# Search across docs
grep -r "parallel" docs/
```

## 📦 Moving Documents

If you need to move scaffolded documents:
```bash
# Move entire docs folder
mv docs/ /path/to/new/location/

# Copy to another project
cp -r docs/ ../other-project/

# Archive for reference
tar -czf project-docs.tar.gz docs/
```

## 🔍 Finding Your Documents

If you forget where you scaffolded:
```bash
# Find all PRD files in your system
find ~ -name "PRD.md" -type f 2>/dev/null

# Find all docs folders
find ~ -type d -name "docs" 2>/dev/null

# Find recent scaffolding
find ~ -name "TASKS.md" -mtime -7  # Modified in last 7 days
```

## ⚠️ Important Notes

1. **Current Directory Matters**: Always check your current directory before scaffolding
   ```bash
   pwd  # Shows where you are
   ```

2. **Don't Scaffold in System Directories**: Avoid running in /, /usr, /etc, etc.

3. **Check Before Overwriting**: The commands will warn before overwriting existing files

4. **Keep Templates Separate**: Don't modify the original scaffolding repo - it's your template source

## 🆘 Troubleshooting

### Documents Created in Wrong Place
```bash
# Move them to correct location
mv docs/ /correct/path/
mv .claude/ /correct/path/
```

### Can't Find Documents
```bash
# Search for signature files
find . -name "PRD.md"
find . -name "TASKS.md"
```

### Want Clean Scaffolding
```bash
# Remove existing docs first
rm -rf docs/ .claude/
# Then scaffold fresh
/scaffold-all "Project" "type"
```

---

Remember: The scaffolding system creates documents wherever you run it. Always `cd` to your desired project directory first!