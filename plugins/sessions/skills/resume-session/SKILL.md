---
name: resume-session
description: "Resume from a previous session summary. Finds the latest session or uses a specified path. Loads context about what was worked on and what to do next."
argument-hint: "[optional: path or session-id]"
---

# Resume Session

Load a previous session summary to continue where you left off.

**Argument**: $ARGUMENTS

---

## Step 1: Find the Session File

### If argument provided:

1. **Full path**: If $ARGUMENTS is a file path, use it directly
2. **Session ID**: If $ARGUMENTS looks like a session ID (e.g., `202602051430-session-csrf-review`), search for it in:
   - `./.claude-sessions/` (project level first)
   - `~/.claude-sessions/` (user level fallback)
3. **Partial match**: If $ARGUMENTS is a partial description (e.g., `csrf`), find the most recent session containing that term

### If no argument provided:

Find the latest session file:

1. Check `./.claude-sessions/` first (if it exists and has files)
2. Fall back to `~/.claude-sessions/`
3. Sort by filename (timestamp) descending
4. Select the most recent file

---

## Step 2: Load and Parse Session

Read the session summary file and extract:

1. **Context**:
   - When the session occurred
   - What project it was for
   - What was being worked on

2. **Previous work**:
   - What was accomplished
   - Files that were changed
   - Key decisions made

3. **Next steps**:
   - Pending tasks from the checklist
   - Any noted follow-ups

---

## Step 3: Present Session Context

Display the loaded session to the user:

```
RESUMING SESSION
---
Session: [session-id]
Date: [date from session]
Project: [project name]

WHAT WE WORKED ON:
[Summary of previous work - keep brief, 2-4 bullet points]

NEXT STEPS TO CONTINUE:
[List the pending tasks/next steps from the session]

FILES INVOLVED:
[List key files that were being worked on]
---
```

---

## Step 4: Offer to Continue

Ask the user how they'd like to proceed:

1. **Continue with next steps**: Start working on the first pending task
2. **Review specific file**: Open a file that was being worked on
3. **Different direction**: User has something else in mind

---

## Step 5: List Available Sessions (if requested or on error)

If the session isn't found, or user asks to see available sessions:

1. List all session files from both locations:
   - `./.claude-sessions/` (project level)
   - `~/.claude-sessions/` (user level)

2. Display in a formatted list:
   ```
   AVAILABLE SESSIONS
   ---
   Project Sessions (./.claude-sessions/):
   1. 202602051430-session-csrf-review.md (Feb 5, 2026 2:30 PM)
   2. 202602041000-session-research-ssrf.md (Feb 4, 2026 10:00 AM)

   User Sessions (~/.claude-sessions/):
   1. 202602030900-session-general-cleanup.md (Feb 3, 2026 9:00 AM)
   ---
   ```

3. Let user select by number or name

---

## Important Rules

1. **Project level first**: Always prefer project-level sessions when in a project
2. **Graceful fallback**: If no sessions found, inform user and offer to start fresh
3. **Validate files exist**: Don't assume files in the session still exist
4. **Keep context loading quick**: Don't read every file mentioned, just present the summary
5. **Match flexibly**: Allow partial matches for session IDs and descriptions
