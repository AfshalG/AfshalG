# 📚 GitHub Profile README Implementation Guide

## 🎯 Overview
This guide explains how the auto-updating GitHub profile README works, how to maintain it, and how to implement it from scratch.

---

## 📂 Project Structure
```
AfshalG/                        # Your GitHub username repository
├── README.md                   # Your profile README (auto-updated)
├── skills.json                 # Your skills database (source of truth)
├── IMPLEMENTATION_GUIDE.md     # This file
└── .github/
    └── workflows/
        └── update-readme.yml   # GitHub Actions automation
```

---

## 🔧 How It Works

### 1. **Skills Database (`skills.json`)**
This is your single source of truth for all technical skills. The GitHub Action reads this file and updates your README automatically.

### 2. **GitHub Action (`.github/workflows/update-readme.yml`)**
- Triggers when you edit `skills.json` or manually
- Reads skills from JSON
- Injects them into README between markers
- Commits and pushes changes automatically

### 3. **README Markers**
The README contains special markers that tell the Action where to inject content:
```markdown
<!--SKILLS_START-->
(auto-generated content goes here)
<!--SKILLS_END-->
```

---

## 📝 Updating Your Skills

### Method 1: Edit `skills.json` (Recommended)
```bash
cd ~/Desktop/AfshalG
code skills.json  # or use any text editor
```

#### Adding a New Skill
Find the appropriate category and add:
```json
{
  "name": "Django",
  "icon": "https://raw.githubusercontent.com/devicons/devicon/master/icons/django/django-original.svg"
}
```

#### Finding Icon URLs
1. **DevIcon Library** (Best source): https://devicon.dev/
   - Search for your tool
   - Right-click icon → Copy image address
   - Use the SVG URL

2. **Simple Icons**: https://simpleicons.org/
   - Search for your tool
   - Use: `https://cdn.jsdelivr.net/npm/simple-icons@v9/icons/[toolname].svg`

3. **Custom Icons**: 
   - Upload PNG/SVG to `assets/` folder in your repo
   - Use: `https://raw.githubusercontent.com/AfshalG/AfshalG/main/assets/your-icon.png`

### Method 2: Direct README Edit (Not Recommended)
Only edit between the markers - but changes will be overwritten next time Action runs!

---

## 🎨 Current Skills Structure

### Categories in `skills.json`:
1. **languages**: Programming languages (Python, TypeScript, etc.)
2. **frameworks**: Web frameworks (React, Next.js, etc.)
3. **ai_ml**: AI/ML tools (PyTorch, TensorFlow, etc.)
4. **databases**: Database systems (MongoDB, MySQL, etc.)
5. **tools**: Dev tools (Docker, Git, VS Code, etc.)

### Example Entry:
```json
{
  "languages": [
    {
      "name": "TypeScript",
      "icon": "https://raw.githubusercontent.com/devicons/devicon/master/icons/typescript/typescript-original.svg"
    },
    {
      "name": "NewLanguage",
      "icon": ""  // Empty icon = shows as comment in README
    }
  ]
}
```

---

## 🚀 Implementing From Scratch

### Step 1: Create GitHub Profile Repository
```bash
# Repository MUST be named exactly as your GitHub username
# Go to github.com/new
# Name: AfshalG (your username)
# ✅ Public
# ✅ Initialize with README
```

### Step 2: Clone Locally
```bash
cd ~/Desktop
git clone https://github.com/AfshalG/AfshalG.git
cd AfshalG
```

### Step 3: Create Project Structure
```bash
# Create directories
mkdir -p .github/workflows

# Create files
touch skills.json
touch .github/workflows/update-readme.yml
```

### Step 4: Setup `skills.json`
```json
{
  "languages": [
    {"name": "Python", "icon": "https://raw.githubusercontent.com/devicons/devicon/master/icons/python/python-original.svg"}
  ],
  "frameworks": [],
  "ai_ml": [],
  "databases": [],
  "tools": []
}
```

### Step 5: Setup GitHub Action
Create `.github/workflows/update-readme.yml`:
```yaml
name: Update README with Resume Skills

on:
  push:
    branches: [main]
    paths:
      - 'skills.json'
  workflow_dispatch:  # Manual trigger

jobs:
  update-readme:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Generate Resume Skills Section
        run: |
          node <<'EOF'
          const fs = require("fs");
          
          const skills = JSON.parse(fs.readFileSync("skills.json", "utf8"));
          const readme = fs.readFileSync("README.md", "utf8");
          
          function renderIcons(items) {
            return items
              .map((s) => {
                if (s.icon && s.icon.trim() !== "") {
                  return `    <img alt="${s.name}" width="40px" src="${s.icon}"/>`;
                } else {
                  return `    <!-- ${s.name} - icon needed -->`;
                }
              })
              .join("\n");
          }
          
          const section = `### 💻 Languages and Tools:
<p align="left">
${renderIcons(skills.languages)}
${renderIcons(skills.frameworks)}
${renderIcons(skills.ai_ml)}
${renderIcons(skills.databases)}
${renderIcons(skills.tools)}
</p>`;
          
          const newReadme = readme.replace(
            /<!--SKILLS_START-->([\s\S]*?)<!--SKILLS_END-->/,
            `<!--SKILLS_START-->\n${section}\n<!--SKILLS_END-->`
          );
          
          fs.writeFileSync("README.md", newReadme);
          EOF

      - name: Commit changes
        run: |
          git config --global user.name "github-actions[bot]"
          git config --global user.email "github-actions[bot]@users.noreply.github.com"
          git add README.md
          git diff --staged --quiet || git commit -m "chore: auto-update skills section"
          git push
```

### Step 6: Setup README Template
```markdown
# Hi 👋, I'm [Your Name]

<!--SKILLS_START-->
(This will be auto-generated)
<!--SKILLS_END-->

### 📊 Top Languages
<img src="https://github-readme-stats.vercel.app/api/top-langs/?username=YourUsername&layout=compact&theme=radical"/>
```

### Step 7: Push Everything
```bash
git add .
git commit -m "Initial setup"
git push origin main
```

---

## 🔄 Workflow Commands

### Update Skills & Push
```bash
cd ~/Desktop/AfshalG
# Edit skills.json
git add skills.json
git commit -m "Update skills"
git push
# GitHub Action runs automatically!
```

### Manual Trigger
1. Go to: https://github.com/AfshalG/AfshalG/actions
2. Click "Update README with Resume Skills"
3. Click "Run workflow" → "Run workflow"

### Pull Latest Changes
```bash
cd ~/Desktop/AfshalG
git pull origin main
```

---

## 🐛 Troubleshooting

### Issue: GitHub Action Not Running
- Check Actions tab for errors
- Ensure workflow file is in `.github/workflows/`
- Verify JSON syntax is valid

### Issue: Icons Not Showing
- Check if URL is accessible
- Use HTTPS URLs only
- Try different icon source

### Issue: Stats Not Loading
- GitHub Stats API might be down
- Check: https://github-readme-stats.vercel.app/api?username=AfshalG
- Wait and retry

### Issue: Merge Conflicts
```bash
git pull origin main --rebase
# Fix conflicts if any
git add .
git rebase --continue
git push
```

---

## 📊 GitHub Stats Parameters

### Top Languages Card
```markdown
![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=AfshalG&layout=compact&langs_count=8&theme=radical)
```

**Parameters you can change:**
- `layout`: `compact`, `default`, `donut`, `donut-vertical`, `pie`
- `langs_count`: Number of languages to show (1-10)
- `theme`: `radical`, `dark`, `gruvbox`, `tokyonight`, `dracula`
- `hide`: Hide specific languages, e.g., `&hide=html,css`
- `exclude_repo`: Exclude specific repos

### Example Customizations:
```markdown
# Donut chart
![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=AfshalG&layout=donut&theme=tokyonight)

# Hide HTML/CSS
![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=AfshalG&hide=html,css&theme=dark)
```

---

## 🎨 Typing Animation Parameters

Current setup:
```markdown
https://readme-typing-svg.demolab.com?font=Fira+Code&weight=500&size=28&duration=4500&pause=1000&color=FF5733&center=true&vCenter=true&width=700&lines=Line1;Line2;Line3
```

**Parameters:**
- `font`: Font family
- `size`: Font size (10-100)
- `duration`: Typing animation duration in ms
- `pause`: Pause between lines in ms
- `color`: Hex color without #
- `lines`: Text lines separated by semicolon
- `width`: Width of the SVG
- `center`: Center text horizontally
- `vCenter`: Center text vertically

---

## 📚 Resources

### Icon Sources
- **DevIcon**: https://devicon.dev/
- **Simple Icons**: https://simpleicons.org/
- **Skill Icons**: https://skillicons.dev/
- **Shields.io**: https://shields.io/

### Documentation
- **GitHub Actions**: https://docs.github.com/en/actions
- **GitHub Stats**: https://github.com/anuraghazra/github-readme-stats
- **Typing SVG**: https://github.com/DenverCoder1/readme-typing-svg

### Examples & Inspiration
- **Awesome GitHub Profiles**: https://github.com/abhisheknaiidu/awesome-github-profile-readme
- **GitHub Profile Generator**: https://gprm.itsvg.in/

---

## 🔐 Security Notes

1. **Never commit sensitive data** to skills.json
2. **Use HTTPS URLs** for all icons
3. **GitHub Actions bot** has limited permissions (only write to repo)
4. **Review Actions logs** periodically for any issues

---

## 📮 Quick Reference

### Add New Skill
1. Edit `skills.json`
2. Add skill with icon URL
3. Save, commit, push
4. Done! Auto-updates in ~30 seconds

### Change Theme
1. Edit `README.md`
2. Find `theme=radical`
3. Change to desired theme
4. Save, commit, push

### Manual Update
1. Go to Actions tab
2. Run workflow manually
3. Check your profile

---

## 💡 Pro Tips

1. **Batch Updates**: Update multiple skills at once before pushing
2. **Test Locally**: Preview README in VS Code before pushing
3. **Icon Fallback**: Leave icon empty for text fallback
4. **Custom Order**: Reorder items in JSON to change display order
5. **Backup**: Keep a local backup of skills.json

---

## 📝 Maintenance Checklist

### Weekly
- [ ] Review and update skills if learned something new
- [ ] Check if all icons are loading properly
- [ ] Verify GitHub Action is running successfully

### Monthly
- [ ] Update achievements section
- [ ] Review and reorganize skill categories
- [ ] Check for new icon sources for missing icons

### Quarterly
- [ ] Major profile refresh
- [ ] Update typing animation text
- [ ] Review overall profile impact

---

## 🆘 Need Help?

1. Check GitHub Actions logs for errors
2. Verify JSON syntax at https://jsonlint.com/
3. Test icon URLs in browser
4. Check GitHub Status: https://www.githubstatus.com/

---

## 🎉 You're All Set!

Your GitHub profile now auto-updates whenever you modify `skills.json`. 
Remember: The profile repository lives at `~/Desktop/AfshalG/`

Last updated: September 2024