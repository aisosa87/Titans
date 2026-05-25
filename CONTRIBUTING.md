# Contributing to TitanBridge

Thank you for being part of Team Titans. This document outlines how to contribute code to TitanBridge.

## Before You Start

1. Read the **README.md** — it has setup instructions and project structure
2. Understand **TitanBridge_Development_Plan.docx** — it defines what you're building
3. You should have access to the **dev** branch and your feature branch

## Branching Strategy

### Main Branches

- **main** — Production. Protected. Only David merges here after full testing.
- **dev** — Integration. All feature branches merge here. This is where daily development happens.

### Feature Branches

Everyone works on their own feature branch:

```
feature/student-portal          (David)
feature/payment-integration     (Aisosa)
feature/admin-infrastructure    (Victory)
```

### Creating Your Feature Branch

```bash
git checkout dev
git pull origin dev
git checkout -b feature/your-feature-name
```

## Making Changes

### 1. Pull the Latest Code

Before you start working, always pull the latest:

```bash
git checkout dev
git pull origin dev
git checkout feature/your-branch
git merge dev
```

This prevents conflicts later.

### 2. Write Your Code

- Follow C# naming conventions (see README.md)
- Write clean, readable code
- Add comments for complex logic
- Test your code locally before pushing

### 3. Commit Frequently

Commit often with clear messages:

```bash
git add .
git commit -m "Add student fee dashboard"
```

Good commit messages describe WHAT you changed and WHY:

```
✅ GOOD:
"Add BankLink webhook verification with signature validation"
"Fix multi-tenant query filter to prevent data leakage"
"Implement receipt PDF generation in background worker"

❌ BAD:
"update code"
"fixes"
"WIP"
```

### 4. Push to GitHub

```bash
git push origin feature/your-branch
```

## Pull Requests (PRs)

### When to Create a PR

Create a PR when:
- A feature is complete (not just "works on my machine")
- Code is tested
- You're ready for code review

### PR Checklist

Before creating a PR, verify:

- [ ] Code builds without errors (`dotnet build`)
- [ ] Tests pass (`dotnet test`)
- [ ] No new merge conflicts with `dev`
- [ ] You've followed naming conventions
- [ ] You've added comments for complex logic
- [ ] You've tested the feature end-to-end

### Creating a PR

1. Go to the GitHub repository
2. Click "New Pull Request"
3. Set:
   - **Base branch:** `dev`
   - **Compare branch:** `feature/your-branch`
4. Fill in the PR description:

```markdown
## What This PR Does

Adds BankLink webhook handler with JWT verification and idempotency.

## Changes

- Implemented POST /webhooks/banklink endpoint
- Added signature verification using shared "key" header
- Implemented idempotency check with Redis
- Added unit tests for webhook handling

## Testing

Tested locally with mock BankLink requests. Verified:
- Valid webhooks are processed
- Duplicate webhooks return same response (idempotency)
- Invalid signatures are rejected

## Related Issues

Closes #12 (BankLink integration)
```

5. Request review from **David** (development lead)
6. Address feedback in follow-up commits
7. Once approved, David will merge

## Code Review

### What David Will Check

- **Design Consistency:** Does the UI match David's design system?
- **Code Quality:** Is the code clean and readable?
- **Correctness:** Does it actually solve the problem?
- **Security:** Especially for payment code (no secrets in logs, proper validation)
- **Testing:** Are edge cases covered?
- **No Merge Conflicts:** Does it merge cleanly with `dev`?

### Responding to Feedback

- Don't take feedback personally — it's about code, not you
- Respond to each comment
- Make changes and push new commits (don't force-push, let the history show the revision)
- Re-request review once changes are made

## Working with Aisosa & Victory

### If Aisosa is Blocking You

You depend on Aisosa's payment service interfaces. If they're not ready:

1. Create a mock `IPaymentService` locally for testing
2. Tell Aisosa you need the interface (name, parameters, returns)
3. Agree on interface signature
4. Code against the interface, swap in real implementation later

### If Victory is Blocking You

You depend on Victory's models and DbContext. If they're not ready:

1. Create mock model classes locally
2. Tell Victory what properties you need
3. Code against mock, swap in real DbContext later

## Running Tests

Before pushing, test your code:

```bash
# Build
dotnet build

# Run all tests
dotnet test

# Run specific test
dotnet test --filter "TestName"
```

## Common Gotchas

### Accidentally Committed Secrets?

Never commit API keys, passwords, or secrets. If you did:

1. **DO NOT** push to GitHub yet
2. Remove the file: `git rm --cached appsettings.Development.json`
3. Add it to `.gitignore`
4. Commit: `git commit -m "Remove secrets from history"`
5. Tell David immediately

### Merge Conflicts?

If `dev` has changed since you branched, you'll see conflicts:

```
<<<<<<< HEAD (your code)
   var x = GetStudentFees();
=======   (dev code)
   var x = GetStudentFeesAsync();
>>>>>>> dev
```

To resolve:

1. Open the file
2. Decide which version to keep
3. Remove the `<<<<<<<`, `=======`, `>>>>>>>` markers
4. Save and commit: `git add . && git commit -m "Resolve merge conflict"`

If unsure, ask David.

### Accidentally on `main` Instead of `dev`?

```bash
# Check what branch you're on
git branch

# If on main, switch to dev
git checkout dev
```

## Communication

### Daily Standups

**Time:** [Set by David]  
**Duration:** 5-10 minutes  
**Format:** 
1. What did you finish yesterday?
2. What's blocking you?
3. What are you working on today?

### Blocking Issues

If you're stuck:

1. **Try to fix it yourself for 15 minutes**
2. **Google the error**
3. **Ask the team** — post in Slack or mention in standup
4. **David escalates if needed**

Don't waste time stuck. Ask for help.

### Code Questions

- Complex logic? Add a comment
- Not sure about approach? Mention in PR or ask in Slack before coding
- Different opinions? Discuss in PR, David decides if needed

## Before You Merge (David Only)

As development lead, David does a final check:

1. ✅ All tests pass
2. ✅ Code review approved
3. ✅ No conflicts with `dev`
4. ✅ Commits are squashed (one commit per feature, or well-organized)
5. ✅ PR description is clear
6. ✅ Merge strategy: "Squash and merge" (keeps `dev` clean)

Then: **Merge to `dev`**, delete the feature branch, pull team's code to stay in sync.

## Questions?

- **How do I...?** → Ask in standup or Slack
- **Is this the right approach?** → Mention in PR, David will advise
- **I broke something** → Tell David immediately, we'll fix together

---

**Remember:** Clean commits, clear PRs, test your code, ask for help early. We're a team.

Welcome to the project. 🚀
