---
argument-hint: "<error-message|file:line>"
description: "Intelligent error analysis and resolution"
model: claude-opus-4-5-20251101
allowed-tools: ["Bash", "Read", "Glob", "Grep", "WebSearch", "AskUserQuestion"]
---

**If `$ARGUMENTS` is empty or not provided:**

Display usage information and ask for input:

**Usage:** `/debug <error-or-location>`

**Examples:**

- `/debug "TypeError: Cannot read property 'map' of undefined"` - Debug an error message
- `/debug src/api/users.ts:45` - Debug code at specific location
- `/debug "connection refused"` - Debug a runtime error
- Paste a full stack trace for analysis

**Workflow:**

1. Parse error message and stack trace
2. Search codebase for related code
3. Analyze likely causes
4. Suggest fixes with examples
5. Link to relevant documentation

Ask the user: "What error are you seeing? Paste the error message or stack trace."

---

**If `$ARGUMENTS` is provided:**

Analyze the error and provide debugging guidance.

## Configuration

- **Input**: `$ARGUMENTS` (error message, stack trace, or file:line reference)

## Steps

1. **Parse the Error**

   Extract from the input:
   - Error type/name (TypeError, NullPointerException, etc.)
   - Error message
   - Stack trace (if provided)
   - File paths and line numbers
   - Variable/function names mentioned

   Common patterns:

   ```text
   TypeError: Cannot read property 'x' of undefined
   → null/undefined access

   ReferenceError: x is not defined
   → variable not in scope

   SyntaxError: Unexpected token
   → parsing/syntax issue

   ECONNREFUSED
   → network/connection issue
   ```text

2. **Locate Relevant Code**

   If file:line provided:

   ```bash
   # Read the specific file
   # Show context around the error line
   ```text

   If error mentions code:

   ```bash
   # Search for the function/variable
   rg "functionName" --type ts
   ```text

   Extract:
   - The problematic code
   - Surrounding context (10-20 lines)
   - Related imports/dependencies

3. **Analyze the Cause**

   Common root causes by error type:

   **Null/Undefined Errors**:
   - Missing null checks
   - Async data not loaded
   - Optional chaining needed
   - Default values missing

   **Type Errors**:
   - Wrong data type passed
   - API response shape changed
   - Type assertion incorrect

   **Reference Errors**:
   - Import missing
   - Variable out of scope
   - Typo in variable name
   - Hoisting issue

   **Network Errors**:
   - Service not running
   - Wrong URL/port
   - CORS issues
   - Timeout

4. **Search for Similar Issues**

   If needed:

   ```bash
   # Search codebase for similar patterns
   rg "similar error handling" --type ts

   # Check for related tests
   rg "test.*functionName" --type ts
   ```text

   Also search web for:
   - Framework-specific solutions
   - Known issues/bugs
   - Stack Overflow answers

5. **Generate Fix Suggestions**

   Provide multiple approaches:

   ```markdown
   ## Error Analysis

   **Error**: TypeError: Cannot read property 'map' of undefined
   **Location**: src/components/UserList.tsx:23

   ### Root Cause

   The `users` variable is undefined when `map()` is called.
   This happens because the API response hasn't loaded yet.

   ### Problematic Code
   ```tsx
   // Line 23
   return users.map(user => <UserCard key={user.id} user={user} />)
   ```text

   ### Fix Options

   #### Option 1: Optional Chaining (Quick Fix)

   ```tsx
   return users?.map(user => <UserCard key={user.id} user={user} />) ?? []
   ```text

   #### Option 2: Early Return (Better)

   ```tsx
   if (!users) return <Loading />
   return users.map(user => <UserCard key={user.id} user={user} />)
   ```text

   #### Option 3: Default Value (Best for this case)

   ```tsx
   const [users, setUsers] = useState<User[]>([])  // Initialize as empty array
   return users.map(user => <UserCard key={user.id} user={user} />)
   ```text

   ### Prevention

   - Add TypeScript strict null checks
   - Initialize state with proper defaults
   - Add loading states for async data

   ```text

6. **Check for Patterns**

   Look for similar issues elsewhere:

   ```bash
   # Find similar patterns that might have same bug
   rg "\.map\(" --type tsx | grep -v "?."
   ```text

   Report if found:

   ```markdown
   ### Similar Patterns Found

   These locations might have the same issue:
   - src/components/PostList.tsx:45
   - src/components/CommentList.tsx:32
   ```text

7. **Provide Context**

   Include:
   - Links to relevant documentation
   - Related GitHub issues if known
   - Best practices for this error type
   - Testing suggestions to prevent recurrence

## Error Categories

| Type | Common Causes | Quick Fixes |
|------|---------------|-------------|
| TypeError | null access | Optional chaining, null checks |
| ReferenceError | missing import | Check imports, scope |
| SyntaxError | typos, missing brackets | Linter, formatter |
| NetworkError | service down, CORS | Check URL, proxy config |
| ValidationError | bad input | Input validation, types |

## Output Format

```markdown
## Debug Report

**Error**: [error message]
**Location**: [file:line]
**Confidence**: High/Medium/Low

### Analysis
[What's causing the error]

### Fix
[Code solution]

### Prevention
[How to avoid in future]

### Related
[Links, docs, similar issues]
```text

## Notes

- Always read the actual code before suggesting fixes
- Consider the broader context (async, state management)
- Suggest defensive coding practices
- Recommend tests to prevent regression
- If error is unclear, ask for more context
