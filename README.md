# Claude Skills

A collection of custom skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

## Skills

### `/make-it-rain`

A celebration skill. When you've crushed a coding session, run `/make-it-rain` and Claude will reflect on what was accomplished and hype up the work with a short, punchy victory lap.

### `/post-dev`

A post-development quality gate. Spawns four parallel agents to run comprehensive checks before handing code back:

1. **Code Review** - Reviews all new/modified code for bugs, security issues, performance concerns
2. **Refactoring Opportunities** - Scans entire touched files for broader improvements (report only)
3. **Documentation Updates** - Walks up the directory tree checking for stale docs
4. **Test Verification** - Runs relevant test suites and fixes failures

## Installation

Copy the skills you want into your Claude Code skills directory:

```bash
# Copy a single skill
cp -r skills/make-it-rain ~/.claude/skills/

# Copy all skills
cp -r skills/* ~/.claude/skills/
```

## Usage

Once installed, invoke skills by typing their name as a slash command in Claude Code:

```
/make-it-rain
/post-dev
```

## License

MIT
