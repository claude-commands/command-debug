# command-debug

A Claude Code slash command for intelligent error analysis and resolution.

## Installation

```bash
# Clone to your preferred location
git clone git@github.com:claude-commands/command-debug.git <clone-path>/command-debug

# Symlink (use full path to cloned repo)
ln -s <clone-path>/command-debug/debug.md ~/.claude/commands/debug.md
```text

## Usage

```text
/debug "TypeError: Cannot read property 'map' of undefined"
/debug src/api/users.ts:45
/debug "connection refused"
```text

Or paste a full stack trace for analysis.

## What it does

1. Parses error messages and stack traces
2. Locates relevant code in codebase
3. Analyzes likely root causes
4. Suggests multiple fix options
5. Finds similar patterns that might have same issue

## Output Format

```markdown
## Error Analysis

**Error**: TypeError: Cannot read property 'map' of undefined
**Location**: src/components/UserList.tsx:23

### Root Cause
The `users` variable is undefined when `map()` is called.

### Fix Options

#### Option 1: Optional Chaining
```tsx
return users?.map(user => <UserCard />) ?? []
```text

#### Option 2: Default Value

```tsx
const [users, setUsers] = useState<User[]>([])
```text

### Similar Patterns Found

- src/components/PostList.tsx:45
- src/components/CommentList.tsx:32

```text

## Error Categories

| Type | Common Causes | Quick Fixes |
|------|---------------|-------------|
| TypeError | null access | Optional chaining |
| ReferenceError | missing import | Check imports |
| SyntaxError | typos | Linter, formatter |
| NetworkError | service down | Check URL, proxy |

## Requirements

- Git repository with source code
- Claude Code with Opus 4.5 model access

## Updates

```bash
cd <clone-path>/command-debug && git pull
```text
