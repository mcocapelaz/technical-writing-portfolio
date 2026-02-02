# Git Panic Station 🚨
## A Survival Guide for Junior Developers

> When things go wrong with Git (and believe me, they will), this is your emergency manual.
> 
> Note: This guide is intentionally exhaustive. For interview review, focus on the structure, tone, and selected sections (Mild Panic, Stash, Nuclear Option).

---

## 📖 Table of Contents

### 🟢 Mild Panic (Quick Fixes)
- [Wrong commit message](#wrong-commit-message)
- [Forgot to run `git pull`](#forgot-git-pull)
- [Staged wrong files with `git add .`](#staged-wrong-files)
- [Confused about `git stash`](#stash-confusion)

### 🟡 Medium Panic (Few Steps Required)
- [Undo a pushed commit (public branch)](#undo-pushed-commit)
- [Committed passwords/API keys/secrets](#committed-secrets)
- [Merge conflicts and I'm panicking](#merge-conflicts)
- [Committed on the wrong branch](#wrong-branch)

### 🔴 Full Panic (Deep Breath Required)
- [Something broke the repo](#broke-repo)
- [Overwrote teammate's code with force-push](#overwrote-teammate)

### 🚨 Nuclear Option
- [Ultimate reset - match remote exactly](#nuclear-option)

### 🛡️ Resources
- [Prevention tips](#prevention-tips)
- [Emergency cheat sheet](#emergency-cheat-sheet)

---

## 🟢 Mild Panic

<a id="wrong-commit-message"></a>
### "I wrote the wrong commit message and pushed it"

**What happened:**
You typed the commit message too fast and now it says "fix bu log" instead of "fix bug in login form". Oops.

**The fix:**

If you HAVEN'T pushed yet:

```bash
git commit --amend
```

This opens your editor. Change the message, save, done ✅

**Want to skip the editor?**

```bash
git commit --amend -m "fix bug in login form"
```

If you **ALREADY** pushed:

```bash
git commit --amend -m "fix bug in login form"
git push --force-with-lease
```

**Bonus tip:** Forgot to include a file in your commit?

```bash
git add missing-file.js
git commit --amend --no-edit  # Adds file without changing message
```

⚠️ **Important:** Only use `--force-with-lease` if you're working alone on this branch, or if you've coordinated with your team. Never force-push to shared branches like main or develop because it could create a confusing situation for your teammates.

**Why this works:**

`--amend` is like using an eraser on your most recent Git action because it rewrites the last commit.

[↑ Back to top](#-table-of-contents)

---

<a id="forgot-git-pull"></a>
### "I forgot to run `git pull`"

**What happened:**
You started coding without pulling the latest changes. Now when you try to push, Git yells at you:

`! [rejected] main -> main (non-fast-forward)`

---

**The fix:**

**Step 1:** Check your current situation

```bash
git status
```

---

**Step 2:** Pull with rebase (cleaner history)

```bash
git pull --rebase origin main
```

**Step 3:** If there are conflicts, don't panic! Git will tell you what to do:

- Fix conflicts in your editor (remove `<<<<<<<` markers)
- Then:

```bash
git add .
git rebase --continue
```

**Step 4:** Push your changes

```bash
git push origin main
```

---

#### Common issue: "But I have uncommitted changes!"

If `git pull --rebase` gives you an error like:

```
error: cannot pull with rebase: You have unstaged changes.
```

**You have two options:**

**Option 1: Commit your changes first** (recommended)

```bash
# Commit what you were working on
git add .
git commit -m "WIP: add validation to login form"

# Now pull
git pull --rebase origin main

# Now push
git push origin main
```

---

**Option 2: Stash temporarily**

```bash
# Hide your changes temporarily
git stash

# Pull the latest
git pull --rebase origin main

# Restore your changes
git stash pop

# Continue working or commit
```

**Which one to use?**

- Use **Option 1** if your changes are at a "saveable" point (even if incomplete)
- Use **Option 2** if you're in the middle of something messy and don't want to commit yet

---

**How to prevent this:**

Always run `git pull` before starting work. Make it a habit!

**Morning routine:** Breakfast → `git pull` → Code 

You can also pull frequently during the day:

```bash
# Every hour or so:
git pull --rebase origin main
```

Small, frequent syncs = fewer conflicts.

---

**Why this works:**

`git pull --rebase` does two things:

1. **Downloads** the latest commits from the remote
2. **Reapplies** your commits on top of them

This keeps your history clean (no messy merge commits) and makes it look like you always had the latest code when you started working.

**Visual example:**

Before pull:

```text
Remote:      A -- B -- C -- D (someone else's work)
Your local:  A -- B -- E -- F (your work)
```

After `git pull --rebase`:

```text
Remote:      A -- B -- C -- D
Your local:  A -- B -- C -- D -- E -- F (your work on top!)
```

Clean and linear! ✨

[↑ Back to top](#-table-of-contents)

---

<a id="staged-wrong-files"></a>
### "I did `git add .` and staged files I didn't want"

**What happened:**
You ran `git add .` and accidentally staged `.env`, passwords, or 500MB of node_modules.

**The fix:**

**Unstage specific files:**

```bash
git reset HEAD .env
git reset HEAD secrets.txt
```

**Unstage EVERYTHING:**

```bash
git reset HEAD
```

**Check what's staged:**

```bash
git status
```

**How to prevent this:**

Use `.gitignore` and `git add` specific files instead of `.`

[↑ Back to top](#-table-of-contents)

---

<a id="stash-confusion"></a>
### "I'm confused about `git stash`"

**What happened:**
You used `git stash` and now you don't know where your changes went, or you ran it multiple times and can't find the right one.

---

#### What is stash?

**Simple explanation:**
Stash is a local and temporary drawer where you hide changes you're not ready to commit yet.

**Why you need it:**
You're coding on `feature-login`, but suddenly need to switch to `main` for an urgent fix. You can't commit half-finished work, so you stash it.

---

#### The basic workflow:

```bash
# You're working on feature-login
# Urgent: need to fix bug on main

git stash  # Hide your changes temporarily
git checkout main  # Switch to main (your changes are safe!)

# Fix the bug, commit, etc.

git checkout feature-login  # Back to your work
git stash pop  # Restore your changes
```

---

#### Essential stash commands:

**1. Save your changes**

```bash
# Basic stash (tracked files only):
git stash

# Include untracked files (new files you haven't added):
git stash -u

# Include everything (even ignored files):
git stash -a

Note: This command stash ignored files, so you can work in your completely clean branch.

# With a descriptive message (RECOMMENDED):
git stash push "WIP: login form validation - needs testing"
```

**Pro tip:** Always use a message. Future you will thank you.

---

**2. See what's in the stash**

```bash
git stash list
```

**Output example:**
```
stash@{0}: WIP on feature-login: work in progress on login form
stash@{1}: WIP on main: 5f3d2a1 quick hotfix
stash@{2}: WIP on feature-api: testing new endpoint
```

- `stash@{0}` = Most recent stash
- `stash@{1}` = Second most recent
- etc.

---

**3. Restore your changes: `pop` vs `apply`**

**This confuses everyone, so read carefully:**

```bash
git stash pop    # Restore + DELETE from stash
git stash apply  # Restore + KEEP in stash
```

**Which one should you use?**

| Scenario | Command | Why |
|----------|---------|-----|
| Normal use (95% of the time) | `git stash pop` | Restores and cleans up automatically |
| Want to apply to multiple branches | `git stash apply` | Keeps the stash so you can reuse it |
| Not sure if it will work | `git stash apply` | Safe to try, doesn't delete the stash |

**Most of the time: use `pop`**

---

**4. Restore a SPECIFIC stash**

```bash
# List your stashes:
git stash list

# Restore a specific one:
git stash pop stash@{2}
git stash apply stash@{1}
```

---

**5. Preview what's inside (without applying)**

```bash
# Summary:
git stash show stash@{0}

# Full diff:
git stash show -p stash@{0}
```

---

**6. Delete stashes**

```bash
# Delete a specific stash:
git stash drop stash@{1}

# Delete ALL stashes (careful!):
git stash clear
```

---

#### Common problems and solutions:

**Problem 1: "I stashed multiple times and I'm lost"**

**Solution:**

1. List everything:

```bash
git stash list
```

2. Preview each one:

```bash
git stash show -p stash@{0}  # Look at what's inside
git stash show -p stash@{1}
git stash show -p stash@{2}
```

3. Apply the one you want:

```bash
git stash pop stash@{1}
```

4. If you want to include untracked files: 

```bash
git stash show -p --include-untracked stash@{0}
```
---

**Problem 2: "`git stash pop` gave me conflicts"**

**What happened:**
The code changed since you stashed, and now there are conflicts.

**Solution:**

Resolve conflicts like a normal merge:

1. Open the files with `<<<<<<<` markers
2. Choose which version to keep
3. Remove the markers
4. Mark as resolved:

```bash
git add resolved-file.js
```

**Note:** If `git stash pop` fails due to conflicts, the stash is NOT automatically deleted. You can either:

- Fix the conflicts and continue (no additional command needed)
- Or drop the stash manually:

```bash
git stash drop stash@{0}
```

---

**Problem 3: "I accidentally deleted my stash"**

**Good news:** Stashes are commits! They're recoverable.

**Solution:**

1. Find all commits (including deleted stashes):

```bash
git reflog show stash
```

2. Try each hash until you find your stash:

```bash
git stash apply abc1234567890
```

4. Or see what's inside:

```bash
git show abc1234567890
```

---

**Problem 4: "I stashed, switched branches, now stash pop doesn't work"**

**What happened:**
You can apply a stash to ANY branch, but it might conflict.

**Solution:**

1. If it conflicts, try `apply` instead of `pop` (keeps the stash):

```bash
git stash apply
```

2. If it's hopelessly broken:

```bash
git stash show -p --include-untracked # Check what's inside first
git checkout original-branch  # Go back to where you stashed
git stash pop  # Try there
```

---

#### Advanced tips:

**Stash specific files only:**

```bash
# Stash only certain files:
git stash push -m "stashing just the config" config.js utils.js

# Stash everything EXCEPT certain files:
git stash push -m "stashing all but README" -- . ':!README.md'
```

---

**Create a branch from a stash:**

```bash
# Instead of applying, create a new branch:
git stash branch new-branch-name stash@{0}
```

This creates a new branch, applies the stash, and drops it from the list.

---

#### Don't use stash as long-term storage:

❌ **Bad:**

```bash
git stash push "maybe I'll need this someday"
# ...weeks later...
# What was that again?
```

✅ **Good:**

If you need to save work for more than a day, commit it on a branch:

```bash
git checkout -b temp-work
git add .
git commit -m "WIP: save progress on feature"
```

---

#### Stash management best practices:

**1. Use descriptive messages:**

```bash
# ❌ Bad:
git stash

# ✅ Good:
git stash push -m "half-done login validation - breaks on empty email"
```

**2. Clean up regularly:**

```bash
# See what you have:
git stash list

# Drop old stashes you don't need:
git stash drop stash@{3}

# Or clear everything (if nothing important):
git stash clear

🚨 Be careful! This command deletes the entire stash list at once. It's extremely difficult to recover anything after a clear . You'd have to run `git fsck --unreachable`.
```

**3. Don't let stashes pile up:**

If you have more than 3-4 stashes, you're probably using it wrong. Commit your work instead.

---

#### Quick reference table:

| Task | Command |
|------|---------|
| Stash changes | `git stash` or `git stash -u` |
| Stash with message | `git stash push -m "mensaje"` |
| List stashes | `git stash list` |
| Restore latest (and delete) | `git stash pop` |
| Restore latest (keep it) | `git stash apply` |
| Restore specific stash | `git stash pop stash@{2}` |
| Preview stash | `git stash show -p -u stash@{0}` |
| Delete specific stash | `git stash drop stash@{1}` |
| Delete all stashes | ⚠️ `git stash clear`  | 
| Recover deleted stash | `git fsck --unreachable` |

---

#### Real-world example:

**Situation:** You're building a login form, but your manager needs a hotfix NOW.

```bash
# You're on feature-login with uncommitted changes
git status
# shows: modified auth.js, new file validation.js

# Stash your work:
git stash -u -m "login form: added email validation"

# Fix the urgent bug:
git checkout main
git pull
# make the fix
git add .
git commit -m "hotfix: resolve login crash"
git push

# Back to your feature:
git checkout feature-login
git stash pop

# Continue working where you left off
```

---

#### When NOT to use stash:

❌ **Don't use stash to:**
- Save work for days/weeks (use a branch)
- Share changes with teammates (use a branch + push)
- Back up your work (commit + push)

✅ **Use stash for:**
- Quick context switching (minutes/hours)
- Testing something without committing
- Temporary cleanup of working directory

---

#### Why this works:

`git stash` creates a temporary commit of your changes and hides it away. When you `pop` or `apply`, Git tries to reapply those changes to your current code. It's like copy-paste for uncommitted work. The stash lives in a special storage area separate from your branches, so you can access it from anywhere.

**How to prevent confusion:**

Use descriptive messages, don't let stashes pile up, and remember: `pop` deletes it (most common), `apply` keeps it.

---

**Remember:** Stash is your friend for quick context switches. It's not scary—it's just a temporary drawer for your work!

[↑ Back to top](#-table-of-contents)

---

## 🟡 Medium Panic

<a id="undo-pushed-commit"></a>
### "I pushed a commit and now I need to undo it (public branch)"

**What happened:**
You pushed a bug to `main` and others have already pulled it. You can't use force-push. What now?

**The fix:**

Use `git revert` - it creates a NEW commit that undoes the bad one:

```bash
# Find the bad commit
git log --oneline

# Revert it (creates opposite commit)
git revert abc1234

# Push the revert
git push origin main
```
⚠️ **Warning:** If Git gives you an error when reverting, it's because you have unsaved changes. Create a `git stash` before attempting the revert to clean up your workspace.

**Why this is safe:**
- Doesn't rewrite history
- Others can pull normally
- Creates audit trail

[↑ Back to top](#-table-of-contents)

---

<a id="committed-secrets"></a>
### "I committed passwords/API keys/secrets"

**What happened:**
You committed `.env`, `config.js` with passwords, or API keys. Now they're in Git history. This is a SECURITY ISSUE.

---

#### If you HAVEN'T pushed yet (Easy fix)

**Step 1:** Remove the file from Git (but keep it locally)

```bash
git rm --cached .env
git rm --cached config/secrets.js
```

**Step 2:** Add to .gitignore

```bash
echo ".env" >> .gitignore
echo "config/secrets.js" >> .gitignore
```

**Step 3:** Commit the removal

```bash
git add .gitignore
git commit -m "remove secrets from git, add to .gitignore"
```

✅ Done! The file is now ignored.

---

#### If you ALREADY pushed (SECURITY EMERGENCY)

🚨 **Critical:** Anyone with access to the repo can see your secrets in the commit history.

**Step 1:** IMMEDIATELY rotate/change ALL credentials

- Change passwords NOW
- Regenerate API keys
- Revoke tokens
- Update database credentials

**Do this FIRST, before anything else.** The secrets are compromised.

**Step 2:** Remove from Git history

**Option A: Simple removal (if it's your last commit)**

```bash
git rm --cached .env
git commit --amend --no-edit 
git push --force-with-lease
```

⚠️ Coordinate with your team before force-pushing.

**Option B: Deep in history (use BFG Repo-Cleaner)**

You'll need:
 
If the secret was pushed several commits ago, don't try to fix it alone with complex tools.

* STOP: Don't keep pushing code
* REPORT: Tell your Tech Lead or Senior Dev immediately
* ROTATE: Change the credentials (passwords/keys) right now
* CLEAN: A Senior will use tools like git filter-repo or BFG to scrub the history safely
```

---

#### Why this is serious:

Once secrets are in Git:
- ❌ They're in history forever (unless actively removed)
- ❌ Anyone who cloned the repo has them
- ❌ GitHub/GitLab caches them
- ❌ Bots scan public repos for secrets automatically and exploit them

**Real consequence:** Your API keys can be stolen and used to rack up bills, access your database, or compromise your users.

---

#### Prevention (CRITICAL):

**1. Use `.gitignore` from day one**

Create `.gitignore` BEFORE your first commit:

```bash
# .gitignore
.env
.env.local
*.pem
*.key
config/secrets.js
credentials.json
*.log
```

**2. Never hardcode secrets**

❌ **Bad:**

```javascript
const API_KEY = "sk_live_abc123xyz789";
const DB_PASSWORD = "mypassword123";
```

✅ **Good:**

```javascript
const API_KEY = process.env.API_KEY;
const DB_PASSWORD = process.env.DB_PASSWORD;
```

**3. Use environment variables**

Store secrets in `.env` file (and add it to `.gitignore`):

```bash
# .env
API_KEY=sk_live_abc123xyz789
DB_PASSWORD=mypassword123
```

**4. Use git-secrets tool**

It blocks the commit if it finds a secret and throws an error:

```bash
# Install
brew install git-secrets

# Set up for your repo
cd your-project
git secrets --install
git secrets --register-aws  # Blocks AWS keys
```

**5. Check before you commit**

```bash
git diff --staged  # Review what you're about to commit
```

---

#### Emergency checklist:

If you committed secrets:
- [ ] Rotate ALL credentials immediately
- [ ] Remove from Git history
- [ ] Add to .gitignore
- [ ] Tell your team
- [ ] Check logs for unauthorized access
- [ ] Update credentials in production

**Remember:** Removing from Git history doesn't undo the exposure. Always rotate credentials first.

**How to prevent this:**

Always check what you're committing:

```bash
git status
git diff --staged
```

Use `.gitignore` from the start of every project.

**Why this works:**

`git rm --cached` removes files from Git's tracking without deleting them from your computer. Combined with `.gitignore`, Git will never track them again. For pushed secrets, your senior supervisor will use BFG or git filter-repo to remove the file from every commit, but the damage is done—always rotate credentials.

[↑ Back to top](#-table-of-contents)

---

<a id="merge-conflicts"></a>
### "I have merge conflicts and I'm panicking"

**What happened:**
You tried to merge, pull, or rebase, and Git shows weird markers like `<<<<<<<` and `>>>>>>>`. You're scared to touch anything. Let's decode this.

---

#### What those markers mean:

When you see this in your file:

```javascript
function login(user) {
<<<<<<< HEAD
  return validateUser(user);
=======
  return authenticateUser(user);
>>>>>>> feature-branch
}
```

**Translation:**
- `<<<<<<< HEAD` = **YOUR** current code (what's on your branch now)
- `=======` = The divider
- `>>>>>>> feature-branch` = **THEIR** code (what you're trying to merge in)

Git is asking: **"Which version do you want to keep?"**

Both people edited the same line, and Git can't decide automatically.

---

#### How to resolve (step by step):

**Step 1:** Don't panic. Check which files have conflicts

```bash
git status
```

You'll see:

```
both modified:   src/auth.js
both modified:   src/config.js
```

**Step 2:** Open a conflicted file in your editor

**Step 3:** Find the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`)

**Step 4:** Decide what to keep:

**Option A: Keep YOUR version (HEAD)**

```javascript
// Delete everything else, keep only this:
function login(user) {
  return validateUser(user);
}
```

**Option B: Keep THEIR version**

```javascript
// Delete everything else, keep only this:
function login(user) {
  return authenticateUser(user);
}
```

**Option C: Keep BOTH (merge manually)**

```javascript
// Combine both approaches:
function login(user) {
  const validated = validateUser(user);
  return authenticateUser(validated);
}
```

**Option D: Write something NEW**

```javascript
// Neither version was right, write fresh code:
function login(user) {
  return processLogin(user);
}
```

**Step 5:** Delete ALL conflict markers

Make sure to remove:
- `<<<<<<< HEAD`
- `=======`
- `>>>>>>> feature-branch`

**Step 6:** Save the file

**Step 7:** Mark as resolved

```bash
git add src/auth.js
```

**Step 8:** Repeat for all conflicted files

**Step 9:** Finish the merge

```bash
# If you were merging:
git commit

# If you were rebasing:
git rebase --continue

# If you were pulling:
git commit  # (pull usually auto-commits)
```
✅ Done! Conflict resolved.

---

#### Quick escape hatches:

**Abort everything and start over:**

```bash
git merge --abort   # If merging
git rebase --abort  # If rebasing
```

Your files go back to how they were before you started.

🚨**Accept all of ONE side (nuclear option):**

```bash
# Keep ALL of your changes:
git checkout --ours .

# Keep ALL of their changes:
git checkout --theirs .

# Then:
git add .
git commit
```

⚠️ **Use with caution:** This accepts EVERY conflict one way. Only use if you're 100% sure.

---

#### Visual tools make this MUCH easier:

**VS Code (built-in):**

When you open a conflicted file, you'll see buttons:
- "Accept Current Change" (keep yours)
- "Accept Incoming Change" (keep theirs)
- "Accept Both Changes" (keep both)
- "Compare Changes" (side-by-side view)

**Other tools:**
- **GitKraken** - Beautiful 3-way merge tool
- **Beyond Compare** - Professional merge tool
- **Meld** - Free visual diff/merge tool

**Pro tip:** Let the tool do the work. Don't edit markers manually if you have a GUI.

---

#### Real example walkthrough:

**Scenario:** You and a teammate both edited `user.js`

**Your version (HEAD):**

```javascript
function getUser(id) {
  return db.findUser(id);
}
```

**Their version (feature-branch):**

```javascript
function getUser(id) {
  return database.fetchUser(id);
}
```

**Git shows you:**

```javascript
function getUser(id) {
<<<<<<< HEAD
  return db.findUser(id);
=======
  return database.fetchUser(id);
>>>>>>> feature-branch
}
```

**You decide:** Their version is better (more descriptive variable name)

**Your resolution:**

```javascript
function getUser(id) {
  return database.fetchUser(id);  // Kept their version, removed markers
}
```

**Finalize:**

```bash
git add user.js
git commit -m "resolve conflict: use database.fetchUser"
```

---

#### Common mistakes to AVOID:

❌ **Don't delete markers and commit the mess:**

```javascript
// This is BROKEN:
function getUser(id) {
<<<<<<< HEAD
  return db.findUser(id);
}
```

❌ **Don't keep both versions accidentally:**

```javascript
// This will run the first line and ignore the second:
function getUser(id) {
  return db.findUser(id);
  return database.fetchUser(id);  // Never runs!
}
```

❌ **Don't blindly accept one side without reading:**

Always check what you're keeping. You might lose important changes.

---

#### Prevention:

**1. Pull/rebase frequently**

```bash
# Every morning:
git pull --rebase origin main
```

Small conflicts are easier than big ones.

**2. Communicate with your team**

"Hey, I'm working on auth.js today" prevents two people editing the same file.

**3. Work on different files when possible**

**4. Commit often**

Small commits = smaller conflicts = easier to resolve.

**5. Review your teammate's PRs**

You'll know what they changed, making conflicts easier to understand.

---

#### When to ask for help:

- If the conflict is in code you don't understand
- If you're not sure which version is correct
- If you've tried 3 times and keep breaking things

**It's okay to ask:** "Hey @teammate, we have a conflict in auth.js. Can you help me figure out which version we need?"

---

#### Why this works:

When Git can't automatically merge two changes, it marks the conflict zone with special markers and asks you to manually decide. Once you choose (and remove the markers), Git continues the merge with your decision. The markers are just Git's way of saying: "You're the human, you decide."

**How to prevent this:**

Pull frequently, communicate with your team, and commit small changes often. Smaller, more frequent syncs mean smaller conflicts.

---

#### Final tips:

✅ **Take your time.** Conflicts aren't a race.
✅ **Test after resolving.** Run your app, run tests.
✅ **Commit with a clear message:** `"resolve conflict: keep new validation logic"`
✅ **Learn from patterns.** If you conflict with the same person often, coordinate better.

**Remember:** Conflicts are normal. They just mean two people were productive. Don't fear them!

[↑ Back to top](#-table-of-contents)

---

<a id="wrong-branch"></a>
### "I committed something on the wrong branch"

**What happened:**
You were supposed to commit on `feature-login` but you're actually on `main`. You realize this AFTER hitting Enter. It seems like the end of the world, but it's fixable.

---

#### Scenario A: You HAVEN'T pushed yet 🟢

This is the easy case. We'll move your commit to the correct branch.

**Step 1:** Find the commit hash

```bash
git log --oneline
```

You'll see something like:

```bash
abc1234 (HEAD -> main) your commit that's on the wrong branch
def5678 previous commit
```

Copy that hash: `abc1234`

**Step 2:** Switch to the CORRECT branch

```bash
git checkout feature-login
```

**Step 3:** "Copy and paste" the commit here

```bash
git cherry-pick abc1234
```

✅ Your commit is now on feature-login!

**Step 4:** Go back to the wrong branch and remove the commit

```bash
git checkout main
git reset --hard HEAD~1
```

**Step 5:** Verify everything is clean

```bash
# On main - commit should be gone
git log --oneline

# On feature-login - commit should be here
git checkout feature-login
git log --oneline
```

✅ Done! Your commit is now on the correct branch.

---

#### Scenario B: You ALREADY pushed 🟡

If you already pushed to the wrong branch AND others might have pulled it:

**Option 1:** If you're working alone or can coordinate with your team

Follow the same steps as Scenario A, but add:

```bash
# After removing from wrong branch:
git push --force-with-lease origin main
```

⚠️ Coordinate with your team first!

**Option 2:** If others already pulled your commit (safest)

Leave it on both branches—it won't break anything:

```bash
# Just cherry-pick to the correct branch
git checkout feature-login
git cherry-pick abc1234
git push origin feature-login
```

The commit will exist on both branches. Not ideal, but safe.

---

**Visual Example**

Before:

```text
main:           A -- B -- C (your commit - WRONG!)
feature-login:  A -- B
```

After cherry-pick + reset:

```text
main:           A -- B (cleaned up)
feature-login:  A -- B -- C (your commit - CORRECT!)
```

**How to prevent this:**

Always check your branch before committing:

```bash
# Make this a habit:
git status  # Shows current branch at the top
```

Or configure your terminal to show the current branch in the prompt. Many developers do this!

**Pro tip:** Use a visual Git tool (GitKraken, SourceTree, or VS Code's Git Graph) to see which branch you're on.

**Why this works:**

`git cherry-pick` copies a commit from one branch to another. Think of it as "copy and paste" for commits:

- It creates a NEW commit on the target branch
- The original commit stays untouched (until you delete it with git reset)
- Same changes, different commit hash

`git reset --hard HEAD~1` removes the last commit from the wrong branch, making it like the mistake never happened.

---

**Alternative:** If you haven't committed yet

If you realize you're on the wrong branch BEFORE committing:

```bash
# Save your changes without committing
git stash

# Switch to the correct branch
git checkout feature-login

# Apply your changes there
git stash pop

# Now commit
git add .
git commit -m "your message"
```

This is cleaner because you don't need cherry-pick at all!

[↑ Back to top](#-table-of-contents)

---

## 🔴 Full Panic

<a id="broke-repo"></a>
### "I did something wrong that broke the repo"

**What happened:**
You tried something new, Git exploded, and now nothing works. Your code is broken. Breathe deeply—you have a time machine in your hands, let's travel to the past!

---

#### Option A: The Time Machine Method (For Major Disasters)

Use this when you need to go back several minutes/commits ago.

**Step 1:** Open your Git time machine:

```bash
git reflog
```

You'll see something like this:

```bash
a1b2c3d (HEAD -> main) HEAD@{0}: commit: attempted merge (Here is the DISASTER!)
e4f5g6h HEAD@{1}: commit: everything was working fine ✅
i7j8k9l HEAD@{2}: commit: added login feature
m1n2o3p HEAD@{3}: pull: fast-forward
```

**Step 2:** Identify the commit BEFORE everything broke

Look for the one where everything was still working (usually `HEAD@{1}` or `HEAD@{2}`)

**Step 3:** Let's imagine that our target is `HEAD@{1}`. It's time to travel back in time!

```bash
git reset --hard HEAD@{1}
```

✅ **Magic!** You're back to when everything worked.

⚠️ **Important:** This DELETES all changes after that point. 

**Example:**

```bash
# Go back to HEAD@{1}
git reset --hard HEAD@{1}

# Verify you're back
git status
```

---

#### Option B: Undo Just the Last Commit

**Three ways to undo, depending on what you want to keep:**

##### 1. Soft Reset (Keep everything staged)

```bash
git reset --soft HEAD~1
```

**What happens:**
- ❌ Commit disappears from history
- ✅ Changes stay staged (ready to commit)
- ✅ Files unchanged

**Use this when:** You want to redo the commit with a better message or add more files.

---

##### 2. Mixed Reset (Keep files, unstage changes)

```bash
git reset --mixed HEAD~1
# or simply:
git reset HEAD~1
```

**What happens:**
- ❌ Commit disappears from history
- ❌ Changes become unstaged
- ✅ Files unchanged

**Use this when:** You want to edit files before committing again.

---

##### 3. Hard Reset (Delete everything)

```bash
git reset --hard HEAD~1
```

**What happens:**
- ❌ Commit disappears from history
- ❌ Staging area cleared
- ❌ Files reset to previous commit

**Use this when:** You want to completely erase the last commit and all its changes.

🚨 **Danger:** This is destructive. Use only if you're 100% sure.

---

#### Quick Comparison Table

| Reset Mode | Commit History | Staging Area | Your Files | When to Use |
|------------|----------------|--------------|------------|-------------|
| `--soft` | ❌ Undo | ✅ Keep staged | ✅ Keep | Redo commit message |
| `--mixed` | ❌ Undo | ❌ Unstage | ✅ Keep | Edit before committing |
| `--hard` | ❌ Undo | ❌ Clear | ❌ **DELETE** | Nuclear option |

---

#### Real Example Scenario

**Situation:** You committed broken code and want to fix it.

```bash
# See what you did
git log --oneline

# Output:
# abc123 (HEAD) broken code that doesn't work
# def456 working version
# ghi789 previous feature

# Undo the broken commit but keep the changes
git reset --mixed HEAD~1

# Now your changes are unstaged
git status

# Fix the code in your editor
# Then commit again
git add .
git commit -m "fix: corrected login bug"
```

---

#### How to prevent this:

- **Work on feature branches** for experiments: `git checkout -b experiment`
- **Commit often** in small chunks—easier to undo
- **Test before committing**: run your code/tests first
- **Use `git status`** frequently to see where you are

---

#### Why this works:

**`git reflog`** records EVERY action you take in Git, even "deleted" commits. It's your safety net—nothing is truly lost.

**`git reset`** moves your branch pointer backward in time:
- `--soft` = Undo commit, keep everything else
- `--mixed` = Undo commit + staging
- `--hard` = Undo EVERYTHING (radical option)

Think of Git having three "layers":
1. **Commit history** (saved snapshots)
2. **Staging area** (what's ready to commit)
3. **Working directory** (your actual files)

`git reset` controls how many layers get affected. That's why `--hard` is so powerful—and so dangerous.

---

#### Still panicking? Try this:

If nothing above worked, you can always:

1. **Make a backup branch** of your current mess:

```bash
git branch backup-before-fix
```

2. **Try the time machine** again:

```bash
git reflog
git reset --hard HEAD@{safe-number}
```

3. **Ask for help** in your team Slack/Discord—everyone's been here!

**Remember:** Git is designed to NOT lose your work. If you committed it at some point, `git reflog` can probably recover it. You'll fix it.

[↑ Back to top](#-table-of-contents)

---

<a id="overwrote-teammate"></a>
### "I pushed and overwrote my teammate's code"

**What happened:**
You did `git push --force` to upload your changes. Suddenly you realize: you just overwrote your teammate's work. Your heart races. This is bad, but fixable.

---

#### STEP 0: COMMUNICATE IMMEDIATELY!

**Before touching ANY commands:**

1. **Message your teammate RIGHT NOW:**

```text
Slack/Discord: @teammate URGENT: I accidentally force-pushed to feature-login and
overwrote your code. DON'T PULL yet. I'm recovering it now. Will update you in 5 min.
```

2. **Tell your team lead** (if it's a shared/critical branch)

3. **Prevent others from pulling** until you fix it

⚠️ **Why this matters:** If your teammate pulls before you fix it, their local copy gets overwritten too, and recovery becomes much harder.

---

#### STEP 1: Find the "deleted" commit

Git remembers everything. Let's find your teammate's code in the reflog:

```bash
git reflog show origin/feature-login
```

You'll see something like:

```text
abc1234 origin/feature-login@{0}: update by push (forced)  ← Your bad push
def5678 origin/feature-login@{1}: update by push           ← Your teammate's code HERE
ghi9012 origin/feature-login@{2}: update by push
```

The hash RIGHT BEFORE your forced push (`def5678`) is your teammate's work.

Copy that hash, for example: `def5678`

---

#### STEP 2: Recover the code in a new branch

Create a "rescue" branch with your teammate's work:

```bash
git checkout -b rescue-teammate-code def5678
```

Now you have a branch with their work intact. Check it:

```bash
git log --oneline  # Verify their commit is here
```

---

#### STEP 3: Fix the remote (two options)

Now you need to restore the remote branch. Choose based on your situation:

**Option A:** The Diplomatic Route (Merge) - RECOMMENDED

Use this if both your work AND your teammate's work are valuable.

```bash
# You're on rescue-teammate-code branch
# Bring in your changes
git merge your-feature-branch

# Resolve any conflicts in your editor
# Then:
git add .
git commit -m "merge: recover teammate's code + my changes"

# Push to fix the remote
git push origin rescue-teammate-code:feature-login
```

Now both sets of changes are integrated safely.

---

**Option B:** Complete Undo (Nuclear Option)

Use this ONLY if your changes were wrong and you want to completely restore your teammate's version.

```bash
# Push the rescue branch to overwrite the remote
git push origin rescue-teammate-code:feature-login --force-with-lease
```

This syntax means: "Push my local rescue-teammate-code to the remote feature-login branch"

⚠️ Use `--force-with-lease` instead of `--force` - it's safer because it fails if someone else pushed in the meantime.

---

#### STEP 4: Communicate again

Update your teammate:

```text
@teammate Fixed! I restored your code on `feature-login`. 
You can pull now. Sorry for the scare!
```

---

**How to prevent this (CRITICAL):**

**1. NEVER use `--force` on shared branches**

```bash
# ❌ NEVER DO THIS on main, develop, or shared feature branches:
git push --force origin main

# ✅ Only force-push to YOUR OWN branches
git push --force-with-lease origin my-personal-branch
```

**2. Use `--force-with-lease` instead of `--force`**

```bash
# ❌ Dangerous:
git push --force

# ✅ Safer:
git push --force-with-lease
```

`--force-with-lease` fails if someone else pushed changes you don't have yet. It prevents accidental overwrites.

**3. Check the REMOTE before force-pushing**

```bash
git fetch
git log origin/feature-branch  # See what's on the remote
```

**4. Create a BACKUP BRANCH before risky operations**

```bash
git branch backup-before-force-push
git push --force-with-lease  # Now you have a safety net
```

---

**Why this works:**

Git's reflog tracks EVERY movement of branch pointers, even on remote branches. When you force-push, Git doesn't delete the old commits—it just moves the pointer. The old commits are still there, floating in Git's history, waiting to be recovered.

The syntax `rescue-branch:remote-branch` tells Git: "Take my local branch rescue-branch and use it to update the remote branch remote-branch." This is how you can push one branch to "become" another.

`--force-with-lease` is a "smart force-push" that checks if the remote has changed since you last fetched. If it has, the push fails with a warning, preventing you from overwriting work you haven't seen yet.

---

**Real-world example:**

Before your force-push:

```text
Remote (origin/feature-login):  A -- B -- C (teammate's commit)
Your local:                     A -- B -- D (your commit)
```

After your bad force-push:

```text
Remote (origin/feature-login):  A -- B -- D (overwrote C!)
Your local:                     A -- B -- D
```

Teammate's commit C is "lost" (but still in reflog!)

After recovery:

```text
Remote (origin/feature-login):  A -- B -- C -- D (both commits!)
Your local:                     A -- B -- C -- D
```

---

**Still worried?**

If this happened on main or another critical branch:

- Don't panic more - the fix is the same
- Loop in your team lead immediately
- Document what happened for post-mortem learning
- Consider branch protection rules on your repo to prevent future force-pushes to critical branches

**Remember:** Everyone makes this mistake eventually. What matters is how you handle it. You've got this!

[↑ Back to top](#-table-of-contents)

---

<a id="nuclear-option"></a>
## 🚨 Nuclear Option: The Ultimate Reset

🚨 **EXTREME DANGER**

**This is the most destructive operation in this guide. Read carefully.**

---

### When to use this (and when NOT to):

**✅ Use this when:**
- Your local repo is completely broken beyond repair
- You want to start fresh, matching the remote exactly
- You're 100% certain you don't need ANY local work
- You've already tried everything else in this guide

**❌ DON'T use this if:**
- You have ANY uncommitted work you might need
- You're not sure what's on the remote
- You can fix the problem with a normal `git reset`
- There's even a 1% chance you'll regret it

---

### What happened:

Your local repository is a complete disaster. Merge conflicts everywhere, broken files, experimental code you don't want—it's chaos. You just want your local copy to look EXACTLY like the remote, destroying everything in the process.

---

### ⏸️ Try these FIRST (less destructive):

Before going nuclear, try:

**Option 1: Discard changes but keep commits**

```bash
git reset --hard HEAD
git clean -f -d  # Without -x, keeps .gitignore files
```

**Option 2: Stash everything (recoverable later)**

```bash
git stash --include-untracked
# Your work is saved in stash, not deleted
```

**Option 3: Create a backup branch**

```bash
git branch backup-before-nuclear-option
# Now try the nuclear option - you have a backup
```

Still want to proceed? Keep reading.

---

### The Nuclear Option: Complete Reset

**What will be PERMANENTLY DELETED:**
- ❌ All uncommitted changes (edited files)
- ❌ All untracked files (new files not added to Git)
- ❌ All local commits not pushed to remote
- ❌ All ignored files (.env, node_modules, build folders, etc.)

**What will remain:**
- ✅ Only files that exist on the remote branch

---

### The Fix:

Run these three commands in order:

```bash
# Step 1: Download latest from remote (doesn't change your files yet)
git fetch origin

# Step 2: Reset your branch to match remote exactly
git reset --hard origin/main

# Step 3: Delete ALL untracked and ignored files
git clean -d -x -f
```

**Replace `main` with your branch name if different** (e.g., `origin/develop`)

---

### What each command does:

#### 1. `git fetch origin`

Downloads the latest state from the remote server without touching your local files yet. Think of it as "checking what's on the server."

**What it does:**
- Updates your local knowledge of what's on the remote
- Doesn't change any of your files
- Safe to run anytime

---

#### 2. `git reset --hard origin/main`

Points your local branch to exactly the same commit as the server, discarding all local commits and changes.

**What it does:**
- ❌ Deletes all uncommitted changes in tracked files
- ❌ Deletes all local commits not on the remote
- ✅ Makes your tracked files look exactly like the remote

**What it keeps:**
- Untracked files (new files you never added to Git)
- Ignored files (.env, node_modules, etc.)

---

#### 3. `git clean -d -x -f`

The final cleanup. Deletes everything Git isn't tracking.

**What the flags mean:**
- `-d` = Delete untracked directories (folders)
- `-x` = Delete even ignored files (⚠️ this includes .env, node_modules!)
- `-f` = Force (required for safety)

**What it deletes:**
- ❌ Untracked files (files you never added)
- ❌ Ignored files (.env, node_modules, build/, etc.)
- ❌ Empty directories

---

### After running these commands:

Your repository will look EXACTLY like the remote. You'll need to:

1. **Reinstall dependencies** (if you deleted node_modules, etc.):

```bash
npm install  # or yarn install, pip install, etc.
```

2. **Recreate your .env file** (if you had one):

```bash
# Copy from a backup or your team's documentation
```

3. **Verify everything works:**

```bash
git status  # Should say "nothing to commit, working tree clean"
```

---

### Why this works:

Git has three "zones":
1. **Remote** (what's on the server)
2. **Local commits** (your branch)
3. **Working directory** (your actual files)

This combination resets ALL THREE to match the remote:
- `git fetch` = Updates your view of the remote
- `git reset --hard` = Moves your branch + resets tracked files
- `git clean -d -x -f` = Deletes untracked/ignored files

It's a complete wipe and restore.

---

### Real example:

**Before (your mess):**

```
Local: A -- B -- C (remote) -- D -- E (your local commits)
Working directory: Modified files, uncommitted changes
Untracked: temp.txt, debug.log
Ignored: .env, node_modules/
```

**After nuclear option:**

```
Local: A -- B -- C (matches remote exactly)
Working directory: Clean, matches remote
Untracked: Gone
Ignored: Gone
```

---

### A less destructive alternative:

If you want to keep ignored files like `.env` and `node_modules`, use:

```bash
git fetch origin
git reset --hard origin/main
git clean -d -f  # Without -x, keeps ignored files
```

This preserves your `.env`, `node_modules`, and other ignored files.

---

### If you regret it:

If you realize you needed something after all:

1. **Check reflog immediately:**

```bash
git reflog
```

2. **Find your old commit** (before the reset):

```
abc1234 HEAD@{1}: reset: moving to origin/main  ← The nuclear option
def5678 HEAD@{2}: commit: your work HERE
```

3. **Recover it:**

```bash
git checkout -b recover-my-work def5678
```

**BUT:** This only works for **committed** work. Uncommitted changes and untracked files are gone forever.

---

### How to prevent needing this:

**1. Commit often**

```bash
# Even if it's messy, commit your work:
git add .
git commit -m "WIP: backup before experimenting"
```

**2. Use branches for experiments**

```bash
git checkout -b experiment
# Now you can always go back to main
```

**3. Stash instead of deleting**

```bash
git stash --include-untracked
# Your work is saved, not deleted
```

**4. Push your work regularly**

```bash
git push origin your-branch
# Now it's backed up on the server
```

---

### Final warning:

This is called the "nuclear option" for a reason.
It's a last resort when everything else has failed.

**Before running these commands:**
- ✅ Triple-check you don't need ANY local work
- ✅ Verify the remote has what you need
- ✅ Consider creating a backup branch first
- ✅ Tell your team if working on a shared branch

Once you run this, there's no going back for uncommitted work. Be absolutely sure.

You've been warned. 💀

[↑ Back to top](#-table-of-contents)

---

<a id="prevention-tips"></a>
## 🛡️ Prevention Tips: Avoid the Panic

**The best way to handle Git emergencies? Don't create them.** Here are habits that will save you hours of panic.

---

### Before You Commit

#### 1. Always check your branch

```bash
git status  # Shows current branch at the top
git branch  # Lists all branches, * marks current one
```

**Pro tip:** Configure your terminal to show the branch name in your prompt. Most developers do this to avoid the "wrong branch" panic.

#### 2. Review what you're about to commit

```bash
git diff           # See unstaged changes
git diff --staged  # See what's about to be committed
git status         # Quick overview
```

**Why this matters:** You'll catch accidental files (`.env`, `node_modules`, passwords) before they're committed.

#### 3. Write descriptive commit messages

```bash
# ❌ Bad (you'll regret this in 2 weeks):
git commit -m "fix"
git commit -m "changes"
git commit -m "asdfasdf"

# ✅ Good (your future self will thank you):
git commit -m "fix: resolve login validation bug in auth.js"
git commit -m "feat: add password reset functionality"
git commit -m "docs: update API endpoint documentation"
```

#### 4. Commit small and often

```bash
# Instead of one giant commit at the end of the day:
git add auth.js
git commit -m "fix: validate email format"

git add styles.css
git commit -m "style: update button colors"
```

**Why:** Small commits are easier to undo, review, and understand later.

---

### Before You Push

#### 1. Pull before you push (ALWAYS)

```bash
git pull --rebase origin main  # Get latest changes first
# Then:
git push origin main
```

**Make this a habit:** Pull → Code → Commit → Pull again → Push

#### 2. Double-check which branch you're pushing to

```bash
git branch  # Confirm you're on the right branch
git status  # Shows current branch

# Then push:
git push origin your-branch-name
```

#### 3. NEVER force-push to shared branches

```bash
# ❌ NEVER DO THIS:
git push --force origin main
git push --force origin develop
git push -f origin shared-feature-branch

# ✅ ONLY on YOUR OWN branches:
git push --force-with-lease origin my-personal-feature
```

**Golden rule:** If more than one person uses the branch, don't force-push.

#### 4. Use `--force-with-lease` instead of `--force`

```bash
# ❌ Dangerous (overwrites blindly):
git push --force

# ✅ Safer (fails if remote changed):
git push --force-with-lease
```

`--force-with-lease` checks if someone else pushed while you were working. If they did, it fails safely instead of overwriting their work.

---

### Daily Habits That Save You

#### 1. Start your day with `git status` and `git pull`

```bash
# Every morning:
cd your-project
git status  # Check if you left uncommitted work
git pull    # Get team's latest changes
```

#### 2. Use feature branches for everything

```bash
# ❌ Don't work directly on main:
git checkout main
# make changes...
git commit -m "new feature"

# ✅ Create a branch for each feature:
git checkout -b feature-user-login
# make changes...
git commit -m "add login form"
```

**Why:** If things break, you can always abandon the branch and start over without affecting `main`.

#### 3. Test before you commit

```bash
# Run your tests/linter:
npm test
npm run lint

# Check your app still works:
npm start  # or python manage.py runserver, etc.

# THEN commit:
git add .
git commit -m "feature complete and tested"
```

#### 4. Use `.gitignore` properly

Create a `.gitignore` file in your project root:

```bash
# .gitignore
node_modules/
.env
.DS_Store
*.log
build/
dist/
.vscode/
__pycache__/
```

**Check it works:**

```bash
git status  # These files shouldn't appear
```

---

### Tools That Help Prevent Mistakes

#### 1. Visual Git tools

Install one of these to SEE what you're doing:

- **Git Graph** - VS Code extension that shows branches visually
- **GitKraken** - Beautiful Git GUI (free for students/open source). Download [here](https://www.gitkraken.com/)
- **SourceTree** - Free Git GUI from [Atlassian](https://www.sourcetreeapp.com/)
- **GitHub Desktop** - Simple GUI from GitHub

**Why:** It's MUCH harder to commit to the wrong branch when you can see it visually.

#### 2. Terminal prompt with branch name

Configure your terminal to always show the current branch:

**For Zsh (Mac/Linux):**

```bash
# Install Oh My Zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Your prompt will show: user@computer ~/project (main) $
```

**For Bash:**

Add to `~/.bashrc`:

```bash
parse_git_branch() {
  git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
}
export PS1="\u@\h \W \$(parse_git_branch) $ "
```

#### 3. Git aliases for safety

Add these to `~/.gitconfig`:

```bash
[alias]
  st = status
  co = checkout
  br = branch
  cm = commit -m
  plog = log --oneline --graph --decorate --all
  unstage = reset HEAD --
  amend = commit --amend --no-edit
```

Now you can use shortcuts:

```bash
git st    # instead of git status
git plog  # pretty log with graph
```

---

### Team Practices

#### 1. Enable branch protection on GitHub/GitLab

Protect your `main` and `develop` branches:

- ✅ Require pull requests for merging
- ✅ Require code reviews
- ✅ Disable force-push
- ✅ Require status checks to pass

**How:** Go to your repo Settings → Branches → Add rule

#### 2. Communicate with your team

```bash
# Before force-pushing ANYTHING:
@team: "I need to force-push to feature-login. 
        Please don't pull for 5 minutes."

# After fixing a mistake:
@team: "Fixed the force-push issue on feature-login. 
        Safe to pull now!"
```

#### 3. Code review checklist

Before approving a pull request, check:
- [ ] No `.env` or secrets committed
- [ ] No huge files (images, videos, binaries)
- [ ] Commit messages are clear
- [ ] Tests pass
- [ ] No `console.log` or debug code left

---

### Emergency Preparation

#### 1. Know your safety commands

Memorize these three:

```bash
git reflog           # Your time machine
git status           # Where am I?
git log --oneline    # What happened?
```

#### 2. Create backups before risky operations

```bash
# Before rebasing, merging, or force-pushing:
git branch backup-before-rebase

# If things go wrong, you can always:
git checkout backup-before-rebase
```

#### 3. Bookmark this guide

When panic strikes, you need quick access:
- Save the URL in your browser bookmarks bar
- Pin it in your team's Slack/Discord
- Keep a local copy on your computer

---

### Mindset Shifts

#### 1. Git almost never loses committed work

If you committed it at some point, `git reflog` can probably recover it. Don't panic immediately.

#### 2. Mistakes are learning opportunities

Everyone forces pushes to the wrong branch eventually. Everyone commits passwords. Everyone breaks their repo. It's part of learning Git.

#### 3. Ask for help early

If you've been stuck for 10+ minutes:
- Ask a teammate
- Ask in your company Slack/Discord
- Search Stack Overflow
- Come back to this guide

**Don't suffer in silence.** Senior developers get stuck too.

---

### Quick Prevention Checklist

**Before every commit:**
- [ ] `git status` - am I on the right branch?
- [ ] `git diff --staged` - am I committing the right files?
- [ ] No secrets, passwords, or `.env` files?
- [ ] Commit message is descriptive?

**Before every push:**
- [ ] `git pull` first?
- [ ] Am I pushing to the right branch?
- [ ] Am I using `--force`? (If yes, stop and think)
- [ ] Have I communicated with my team?

---

**Remember:** Prevention is easier than recovery. Build these habits now, and future you will be grateful!

[↑ Back to top](#-table-of-contents)

---

<a id="emergency-cheat-sheet"></a>
## ⚡ Emergency Cheat Sheet

Copy-paste this into your notes for quick reference during panic.

---

### 🟢 Quick Fixes (Under 30 seconds)

```bash
# Wrong commit message (not pushed yet):
git commit --amend -m "correct message"

# Wrong commit message (already pushed):
git commit --amend -m "correct message"
git push --force-with-lease

# Unstage files:
git restore --staged file.js        # Specific file
git restore --staged .                # All files

# Undo uncommitted changes:
git restore file.js      # Specific file
git restore .           # All files

# See what you're about to commit:
git diff --staged
```

---

### 🟡 Medium Fixes (2-5 minutes)

```bash
# Undo last commit (keep changes):
git reset --soft HEAD~1

# Undo last commit (delete changes):
git reset --hard HEAD~1

# Committed on wrong branch:
git log --oneline              # Copy commit hash
git checkout correct-branch
git cherry-pick abc1234
git cherry-pick original-branch # Grabs the last commit from the other branch
git checkout wrong-branch
git reset --hard HEAD~1  # Removes it from the wrong place

# Undo a pushed commit (safe way):
git revert abc1234 # Creates a NEW commit that undoes the changes   
git push origin main 

# Stash work and switch branches:
git stash            # Saves your messy work to a secret vault
git checkout other-branch
# do work...
git checkout original-branch
git stash pop          # Brings your work back and cleans the vault
```

---

### 🔴 Emergency Fixes (5-15 minutes)

```bash
# Time machine (find any past state):
git reflog
git reset --hard HEAD@{2} # Jump back to where you were 2 actions ago

# Recover deleted branch:
git reflog    # Find the hash where the branch was alive
git checkout -b recovered-branch abc1234

# Fix force-push that overwrote teammate:
git reflog show origin/branch-name   # Find the state before your push
git checkout -b rescue def5678 # Create rescue branch at that point
git push origin rescue:branch-name --force-with-lease

# Resolve merge conflicts:
git status                    # See conflicted files
# Edit files, remove <<<<<<< markers
git add .
git commit

*Note: If you're in the middle of a merge, the standard command to finish is `git merge --continue`. It's cleaner because Git already knows you're finishing a merge and fills in the message automatically.
```

---

### 🚨 Nuclear Option (Last Resort)

```bash
# Create backup first:
git branch backup-before-nuclear
# Then run above commands

# Reset to match remote exactly (DELETES EVERYTHING):
git fetch origin
git reset --hard origin/main
git clean -d -x -n  #See what will be deleted 
git clean -d -x -f
```

---

### 🛡️ Safety Commands

```bash
# Check status before doing anything:
git status
git log --oneline
git branch

# Create backup branch before risky operation:
git branch backup-just-in-case

# See full history (including "deleted" commits):
git reflog

# Abort operations in progress:
git merge --abort
git rebase --abort
git cherry-pick --abort
```

---

### 🔍 Investigation Commands

```bash
# What's on the remote?
git fetch
git log origin/main

# What changed in last commit?
git show

# Who changed this line?
git blame file.js

# Find when bug was introduced:
git bisect start
git bisect bad              # Current version has bug
git bisect good abc1234     # This old commit was fine
git bisect reset            # Return to normal 
# Git will help you find the breaking commit

# Search commit messages:
git log --grep="login"

# Search code changes:
git log -S"function login"
```

---

### ⚙️ Useful Configurations

```bash
# Make Git output colorful:
git config --global color.ui auto

# Set default editor:
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "vim"          # Vim

# Set your identity:
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# See all your config:
git config --list

# Create aliases:
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.unstage 'restore --staged'
```

---

### 📋 Branch Management

```bash
# List all branches:
git branch                    # Local branches
git branch -r                 # Remote branches
git branch -a                 # All branches

# Create and switch to new branch:
git switch -c new-branch

# Delete branch:
git branch -d branch-name     # Safe delete (warns if unmerged)
git branch -D branch-name     # Force delete

# Delete remote branch:
git push origin --delete branch-name

# Rename current branch:
git branch -m new-name

# See which branches are merged:
git branch --merged
git branch --no-merged
git fetch --prune     #Removes "ghost" branches from your local list

Note: Never delete a branch before pushing your work or creating a backup branch if you're unsure
```

---

### 🔄 Syncing with Remote

```bash
# Download latest (doesn't change your files):
git fetch origin

# Download and merge:
git pull origin main

# Download and rebase (cleaner history):
git pull --rebase origin main

# Push to remote:
git push origin branch-name

# Push new branch to remote:
git push -u origin branch-name

# See what's different from remote:
git fetch
git diff origin/main
```

---

### 🎯 Quick Reference: Reset Modes

| Command | Commit History | Staging Area | Working Files |
|---------|----------------|--------------|---------------|
| `git reset --soft HEAD~1` | ❌ Undo | ✅ Keep staged | ✅ Keep |
| `git reset --mixed HEAD~1` | ❌ Undo | ❌ Unstage | ✅ Keep |
| `git reset --hard HEAD~1` | ❌ Undo | ❌ Clear | ❌ DELETE |

---

### 💡 When to Use What

**Use `git revert` when:**
- Commit is already pushed
- Others have pulled your changes
- You want to keep history intact

**Use `git reset` when:**
- Commit is NOT pushed yet
- You're working alone on the branch
- You want to completely remove the commit

**Use `git cherry-pick` when:**
- You committed to the wrong branch
- You want to copy a commit to another branch

**Use `git stash` when:**
- You need to switch branches quickly
- You're not ready to commit yet
- You want to try something without committing

---

### 🆘 Panic Mode Quick Decisions

**"I committed the wrong files"**
→ `git reset --soft HEAD~1` → fix → commit again

**"I pushed to the wrong branch"**
→ Cherry-pick to correct branch → coordinate with team

**"I have merge conflicts"**
→ `git status` → edit files → remove markers → `git add .` → `git commit`

**"Everything is broken"**
→ `git reflog` → `git reset --hard HEAD@{safe-number}`

**"I force-pushed by accident"**
→ `git reflog show origin/branch` → create rescue branch → push back

**"I don't know what I did"**
→ `git reflog` → study the history → ask for help

---

### 📱 Keep This Handy

Save these three magic commands:

```bash
git reflog              # Your safety net
git status              # Your compass
git log --oneline       # Your map
```

When in doubt, run all three and study the output. They'll tell you where you are and how to get back.

---

**Remember:**
- Git is designed to NOT lose your work
- Almost everything is recoverable
- You're not the first person to make this mistake
- Ask for help if stuck for more than 10 minutes
- This guide will be here when you need it

**You've got this!** 💪

[↑ Back to top](#-table-of-contents)

---

## 🎓 Final Words

Git can be scary. The command line can be intimidating. Error messages can be cryptic. That's normal.

But here's the truth: **Every senior developer you admire has broken their Git repo at least once.** They've all committed passwords, force-pushed to main, and overwrote someone's code. The difference? They learned how to fix it.

This guide is your safety net. Bookmark it. Share it with your team. Come back to it whenever you panic.

And remember:

✅ **Git almost never loses committed work** - `git reflog` is your time machine

✅ **Mistakes are opportunities to learn** - you'll remember this forever now

✅ **Your team has been there** - don't be afraid to ask for help

✅ **Prevention beats recovery** - build good habits early

You're going to be fine. You're going to fix this. And in a few months, you'll be the one helping someone else out of a Git panic.

**Happy coding!** 🚀

---

## 📚 Additional Resources

- [Official Git Documentation](https://git-scm.com/doc)
- [Oh Shit, Git!?!](https://ohshitgit.com/) - Another panic guide
- [Learn Git Branching](https://learngitbranching.js.org/) - Interactive tutorial
- [Git Cheat Sheet by GitHub](https://education.github.com/git-cheat-sheet-education.pdf)
- [Pro Git Book](https://git-scm.com/book/en/v2) - Free comprehensive guide

---

## 🤝 Contributing

Found a mistake? Have a Git panic scenario that's not covered?

This guide is meant to help junior developers. If you have suggestions, improvements, or additional panic scenarios, contributions are welcome!

**Find me on:**

* LinkedIn: [Mónica Coca](https://www.linkedin.com/in/mcocapelaz) 
* GitHub: [@mcocapelaz](https://github.com/mcocapelaz/)

---

## 📄 License

This guide is free to use, share, and modify. Help other developers avoid the panic!

---

[↑ Back to top](#-table-of-contents)
```

***



