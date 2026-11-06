# Git Complete Cheat Sheet (မြန်မာဘာသာ)

> A practical Git reference guide covering Merge Strategies, Branching, Time Travel, and Rebase vs Merge.
>
> **Language:** Myanmar (မြန်မာ) + English Technical Terms  
> **Level:** Beginner to Intermediate  
> **Goal:** Understand Git deeply and use it confidently in real-world development.

---

# Table of Contents

1. [Git Merge Strategies](#1-git-merge-strategies)
2. [Git Branching Cheat Sheet](#2-git-branching-cheat-sheet)
3. [Git Time Travel](#3-git-time-travel)
4. [Git Rebase vs Merge](#4-git-rebase-vs-merge)
5. [Common Git Mistakes](#5-common-git-mistakes)
6. [Quick Command Reference](#6-quick-command-reference)

---

# 1. Git Merge Strategies

## 1.1 What is Git Merge?

**Git Merge** ဆိုတာ Branch နှစ်ခု (သို့မဟုတ်) နှစ်ခုထက်ပိုတဲ့ Branch တွေမှာ ရှိနေတဲ့ Changes တွေကို ပေါင်းစည်းပေးတာ ဖြစ်ပါတယ်။

ဥပမာ -

ကိုယ်က `main` branch မှာ အလုပ်လုပ်နေတယ်။ နောက်ထပ် Feature တစ်ခုလုပ်ဖို့ `feature/login` branch ခွဲလိုက်တယ်။

```text
main
  |
  A
  |
  B
  |
  C
```

Feature branch မှာ အလုပ်လုပ်မယ်။

```text
main:       A --- B --- C

feature:                \
                         D --- E
```

Feature ပြီးသွားတဲ့အခါ `feature/login` ထဲက Changes တွေကို `main` ထဲ ပေါင်းချင်တယ်။

အဲဒါကို Merge လို့ခေါ်တယ်။

## 1.2 Fast-Forward Merge

**Fast-forward merge** ဆိုတာ Target branch မှာ Merge လုပ်မယ့် Branch နဲ့ကွဲထွက်ပြီးနောက်ပိုင်း New Commit မရှိတဲ့အခါ ဖြစ်နိုင်တဲ့ Merge နည်းလမ်းဖြစ်ပါတယ်။

ဥပမာ -

```text
A --- B --- C (main)
             \
              D --- E (feature/login)
```

ဒီအချိန်မှာ `main` က `C` မှာပဲ ရှိနေတယ်။

`feature/login` က `C` ရဲ့ နောက်မှာ Commit အသစ်တွေ ထပ်လုပ်ထားတယ်။

ဒါကြောင့် Git က Merge Commit အသစ်တစ်ခု ဖန်တီးစရာမလိုဘဲ `main` pointer ကို `E` ဆီ ရွှေ့ပေးနိုင်တယ်။

After merge:

```text
A --- B --- C --- D --- E (main, feature/login)
```

**Command:**

```bash
git switch main
git merge feature/login
```

Fast-forward ဖြစ်နိုင်ရင် Git က အလိုအလျောက် Fast-forward လုပ်ပေးမယ်။

**Advantages:**
- Commit history ရှင်းလင်းတယ်။
- Extra merge commit မလိုဘူး။
- Simple ဖြစ်တယ်။

**Disadvantages:**
- Feature branch တစ်ခုအနေနဲ့ ဘယ်အချိန်မှာ အလုပ်လုပ်ခဲ့လဲဆိုတာ History မှာ Merge Commit နဲ့ သီးသန့်မမြင်ရဘူး။

**When to use:**
- Small features.
- Simple changes.
- Linear commit history လိုချင်တဲ့အခါ။

## 1.3 Three-Way Merge

**Three-way merge** ဆိုတာ Branch နှစ်ခုမှာ သီးခြား Commit အသစ်တွေ ရှိနေတဲ့အခါ Git က Common Ancestor ကို အသုံးပြုပြီး Changes တွေကို ပေါင်းစည်းတာ ဖြစ်ပါတယ်။

ဥပမာ -

```text
             D --- E (feature/login)
            /
A --- B --- C
            \
             F --- G (main)
```

ဒီမှာ -

- Common Ancestor = C
- Feature Branch Latest Commit = E
- Main Branch Latest Commit = G

Git က Commit သုံးခုကို နှိုင်းယှဉ်တယ်။

1. Common Ancestor (C)
2. Feature Branch (E)
3. Target Branch (G)

ပြီးရင် Changes တွေကို ပေါင်းစည်းပြီး Merge Commit အသစ်တစ်ခု ဖန်တီးပေးတယ်။

After merge:

```text
             D --- E
            /       \
A --- B --- C --- F --- G --- M (main)
```

`M` = Merge Commit

**Command:**

```bash
git switch main
git merge feature/login
```

ဒီအခြေအနေမှာ Fast-forward မဖြစ်နိုင်တော့ဘူး။ Git က ပုံမှန်အားဖြင့် Three-way merge လုပ်မယ်။

**Advantages:**
- Feature branch history ကို ထိန်းထားနိုင်တယ်။
- Branch နှစ်ခုလုံးရဲ့ History ကို မြင်နိုင်တယ်။
- Feature တစ်ခုလုံးကို Merge Commit နဲ့ ခွဲခြားနိုင်တယ်။

**Disadvantages:**
- Commit history မှာ Merge Commit တွေ များလာနိုင်တယ်။
- History က ရှုပ်ထွေးလာနိုင်တယ်။

**When to use:**
- Team development.
- Long-running feature branches.
- Feature history ကို ထိန်းထားချင်တဲ့အခါ။

## 1.4 Squash Merge

**Squash merge** ဆိုတာ Feature Branch ထဲက Commit အများကြီးကို Commit တစ်ခုတည်းအဖြစ် ပေါင်းပြီး Target Branch ထဲ ထည့်ပေးတာ ဖြစ်ပါတယ်။

ဥပမာ -

Before merge:

```text
main:       A --- B --- C

feature:                \
                         D --- E --- F --- G
```

Feature branch ထဲမှာ Commit လေးခု ရှိတယ်။

Squash merge လုပ်ပြီးတဲ့အခါ -

```text
main: A --- B --- C --- S
```

`S` = Squashed Commit

Commit လေးခုလုံးရဲ့ Changes တွေကို Commit တစ်ခုတည်းအဖြစ် သိမ်းပေးတယ်။

**Command:**

```bash
git switch main
git merge --squash feature/login
git commit -m "feat: add login feature"
```

သတိပြုရန် - `git merge --squash` က Commit ကို အလိုအလျောက် မဖန်တီးပေးပါဘူး။ ကိုယ်တိုင် `git commit` လုပ်ရပါတယ်။

**Advantages:**
- Main branch History ရှင်းလင်းတယ်။
- Feature တစ်ခုကို Commit တစ်ခုတည်းနဲ့ ကိုယ်စားပြုနိုင်တယ်။
- Small commits အများကြီးကို Main ထဲ မထည့်ရတော့ဘူး။

**Disadvantages:**
- Original Feature Branch Commit History ကို Main Branch ထဲမှာ မထိန်းထားဘူး။
- Individual commits တွေကို Main History ကနေ တိုက်ရိုက်မမြင်ရဘူး။

**When to use:**
- Pull Requests.
- Feature branch မှာ Commit အများကြီး လုပ်ထားတဲ့အခါ။
- Clean Main Branch History လိုချင်တဲ့အခါ။

## 1.5 Merge Strategies vs Merge Options

ဒီနှစ်ခုကို မရောထွေးဖို့ အရေးကြီးတယ်။

Git မှာ Merge လုပ်တဲ့အခါ အသုံးပြုတဲ့ Strategy နဲ့ Merge Option တွေ ရှိတယ်။

### Merge Options

| Command               | Meaning                                    |
| --------------------- | ------------------------------------------ |
| `git merge --ff`      | Fast-forward ဖြစ်နိုင်ရင် Fast-forward လုပ်မယ်      |
| `git merge --no-ff`   | Fast-forward ဖြစ်နိုင်ရင်တောင် Merge Commit ဖန်တီးမယ် |
| `git merge --ff-only` | Fast-forward ဖြစ်နိုင်မှ Merge လုပ်မယ်              |
| `git merge --squash`  | Changes တွေကို Squash လုပ်ပြီး Stage လုပ်ပေးမယ်        |

### Merge Algorithms / Strategies

| Strategy    | Meaning                                                 |
| ----------- | ------------------------------------------------------- |
| `ort`       | Modern default strategy for ordinary two-branch merges  |
| `recursive` | Older default strategy, replaced by `ort` in modern Git |
| `octopus`   | Merge လုပ်မယ့် Branch အများကြီးအတွက်                             |
| `ours`      | Current branch ရဲ့ content ကိုသာ အသုံးပြုမယ်                     |
| `subtree`   | Subtree-related merge handling                          |

**Important:**

`--no-ff` နဲ့ `--squash` က Merge Strategy တစ်ခုတည်း မဟုတ်ဘူး။ Merge လုပ်တဲ့ပုံစံကို ထိန်းချုပ်တဲ့ Options တွေ ဖြစ်တယ်။

### --no-ff Example

```bash
git switch main
git merge --no-ff feature/login
```

Fast-forward ဖြစ်နိုင်တဲ့အခြေအနေမှာတောင် Merge Commit အသစ် ဖန်တီးပေးမယ်။

Before:

```text
A --- B --- C (main)
             \
              D --- E (feature)
```

After:

```text
A --- B --- C -------- M (main)
             \        /
              D --- E (feature)
```

### --ff-only Example

```bash
git switch main
git merge --ff-only feature/login
```

Fast-forward ဖြစ်နိုင်မှသာ Merge လုပ်မယ်။

Fast-forward မဖြစ်နိုင်ရင် Merge ကို ရပ်တန့်မယ်။

---

# 2. Git Branching Cheat Sheet

## 2.1 What is a Branch?

**Branch** ဆိုတာ Project ရဲ့ Development History ထဲမှာ သီးခြားအလုပ်လုပ်နိုင်ဖို့ အသုံးပြုတဲ့ Pointer တစ်ခုဖြစ်တယ်။

ဥပမာ -

```text
main
 |
 A
 |
 B
```

Feature အသစ်တစ်ခုလုပ်ဖို့ Branch ခွဲမယ်။

```text
main: A --- B
            \
feature:     C --- D
```

ဒီလိုဆိုရင် `main` ကို မထိခိုက်စေဘဲ Feature Branch မှာ အလုပ်လုပ်နိုင်တယ်။

## 2.2 List Branches

Local Branch အားလုံးကို ကြည့်မယ်။

```bash
git branch
```

Local + Remote Branch အားလုံးကို ကြည့်မယ်။

```bash
git branch -a
```

Remote Branch တွေကိုပဲ ကြည့်မယ်။

```bash
git branch -r
```

Current Branch ကို ကြည့်မယ်။

```bash
git branch --show-current
```

## 2.3 Create Branch

Branch အသစ်ဖန်တီးမယ်။

```bash
git branch feature/login
```

ဒီ Command က Branch အသစ်ဖန်တီးပေးတယ်။ ဒါပေမဲ့ Current Branch ကို မပြောင်းပေးဘူး။

Create + Switch တစ်ခါတည်း လုပ်မယ်။

```bash
git switch -c feature/login
```

Old syntax:

```bash
git checkout -b feature/login
```

Recommended: `git switch` ကို Branch ပြောင်းဖို့ အသုံးပြုပါ။

## 2.4 Switch Branch

```bash
git switch main
```

Feature Branch ကို ပြောင်းမယ်။

```bash
git switch feature/login
```

Old syntax:

```bash
git checkout main
```

## 2.5 Rename Branch

Current Branch ကို Rename လုပ်မယ်။

```bash
git branch -m new-branch-name
```

အခြား Branch ကို Rename လုပ်မယ်။

```bash
git branch -m old-name new-name
```

## 2.6 Delete Branch

Local Branch ကို Delete လုပ်မယ်။

```bash
git branch -d feature/login
```

`-d` က Safe Delete ဖြစ်တယ်။

Git က Merge မလုပ်ရသေးတဲ့ Changes တွေ ရှိနေတယ်ဆိုရင် Delete မလုပ်ခိုင်းဘူး။

Force Delete:

```bash
git branch -D feature/login
```

`-D` က Merge မလုပ်ရသေးရင်တောင် Delete လုပ်ပေးတယ်။

သတိထားပါ။ Unmerged work ပျောက်သွားနိုင်တယ်။

## 2.7 Push Branch to Remote

Branch အသစ်ဖန်တီးပြီး GitHub ကို Push လုပ်မယ်။

```bash
git push -u origin feature/login
```

`-u` က Upstream Branch ကို သတ်မှတ်ပေးတယ်။

နောက်တစ်ခါဆို -

```bash
git push
```

လို့ပဲ ရတယ်။

## 2.8 Delete Remote Branch

```bash
git push origin --delete feature/login
```

Remote Tracking References တွေကို Update လုပ်မယ်။

```bash
git fetch --prune
```

## 2.9 Branching Workflow

Real-world development မှာ အောက်ပါပုံစံကို အသုံးပြုနိုင်တယ်။

```text
main
 |
 |--- feature/login
 |
 |--- feature/register
 |
 |--- fix/navbar-bug
 |
 |--- feature/profile
```

Example workflow:

**Step 1: Update main**

```bash
git switch main
git pull origin main
```

**Step 2: Create Feature Branch**

```bash
git switch -c feature/login
```

**Step 3: Make Changes**

```bash
git add .
git commit -m "feat: implement login"
```

**Step 4: Push Feature Branch**

```bash
git push -u origin feature/login
```

**Step 5: Create Pull Request**

GitHub မှာ Feature Branch ကနေ Main Branch ကို Pull Request ဖွင့်မယ်။

**Step 6: Merge Pull Request**

Team Review ပြီးတဲ့အခါ Merge လုပ်မယ်။

**Step 7: Update Local Main**

```bash
git switch main
git pull origin main
```

**Step 8: Delete Local Feature Branch**

```bash
git branch -d feature/login
```

## 2.10 Branch Naming Convention

| Prefix      | Purpose              | Example                 |
| ----------- | -------------------- | ----------------------- |
| `feature/`  | Feature အသစ်          | `feature/user-auth`     |
| `fix/`      | Bug Fix              | `fix/login-error`       |
| `hotfix/`   | Production အရေးပေါ် Fix | `hotfix/payment-crash`  |
| `refactor/` | Code Refactoring     | `refactor/auth-service` |
| `docs/`     | Documentation        | `docs/api-guide`        |
| `test/`     | Testing              | `test/login-flow`       |
| `chore/`    | Maintenance          | `chore/update-deps`     |

---

# 3. Git Time Travel

## 3.1 What is Git Time Travel?

Git Time Travel ဆိုတာ Project ရဲ့ အရင်က Commit တွေ၊ Changes တွေကို ပြန်ကြည့်နိုင်တယ်၊ ပြန်သွားနိုင်တယ်၊ ပြန်ယူနိုင်တယ်ဆိုတဲ့ သဘောတရားဖြစ်တယ်။

ဥပမာ -

```text
A --- B --- C --- D --- E (main)
```

ကိုယ်က `E` မှာ ရှိနေတယ်။

ဒါပေမဲ့ `C` မှာ ရှိခဲ့တဲ့ Code ကို ပြန်ကြည့်ချင်တယ်။

Git က အဲဒီလို ပြန်သွားနိုင်အောင် လုပ်ပေးတယ်။

Git မှာ အဓိကအားဖြင့် -

- `git log`
- `git checkout`
- `git switch`
- `git restore`
- `git reset`
- `git revert`
- `git reflog`

တို့ကို သိထားဖို့ လိုတယ်။

## 3.2 View Commit History

Commit History ကြည့်မယ်။

```bash
git log
```

One-line History:

```bash
git log --oneline
```

Graph နဲ့ ကြည့်မယ်။

```bash
git log --oneline --graph --all
```

Detailed History:

```bash
git log --stat
```

နောက်ဆုံး Commit တစ်ခုကို ကြည့်မယ်။

```bash
git log -1
```

## 3.3 Checkout a Previous Commit

Commit ID ကို အသုံးပြုပြီး အရင် Commit တစ်ခုကို ပြန်သွားနိုင်တယ်။

```bash
git checkout <commit-hash>
```

Example:

```bash
git checkout a1b2c3d
```

ဒါပေမဲ့ ဒီလိုလုပ်လိုက်ရင် **Detached HEAD State** ထဲ ရောက်သွားမယ်။

### What is Detached HEAD?

ပုံမှန်အခြေအနေမှာ HEAD က Branch တစ်ခုကို ညွှန်ပြတယ်။

```text
HEAD
 |
main
 |
 A --- B --- C
```

Detached HEAD မှာတော့ HEAD က Branch မဟုတ်ဘဲ Commit ကို တိုက်ရိုက် ညွှန်ပြတယ်။

```text
A --- B --- C (main)
       |
      HEAD
```

ဒီအခြေအနေမှာ Code ကို ကြည့်နိုင်တယ်၊ စမ်းသပ်နိုင်တယ်။

ဒါပေမဲ့ Commit အသစ်တွေ လုပ်ပြီး Branch မဖန်တီးထားဘူးဆိုရင် အဲဒီ Commit တွေကို နောက်ပိုင်း ရှာဖွေရခက်နိုင်တယ်။

ပြန်ထွက်ချင်ရင် -

```bash
git switch main
```

အရင် Commit မှာပဲ အလုပ်ဆက်လုပ်ချင်ရင် -

```bash
git switch -c experiment
```

ဒီလိုဆို Branch အသစ်နဲ့ အဲဒီ Commit ကနေ ဆက်လုပ်နိုင်တယ်။

## 3.4 git restore

`git restore` က Working Directory ထဲက Changes တွေကို ပြန်ပြင်ဖို့ အသုံးပြုတယ်။

ဥပမာ -

File တစ်ခုကို ပြင်လိုက်တယ်။

```text
app.js
```

ဒါပေမဲ့ ပြင်ထားတာတွေကို မလိုချင်တော့ဘူး။

```bash
git restore app.js
```

ဒီ Command က နောက်ဆုံး Commit ထဲက File Content ကို ပြန်ယူပေးတယ်။

**Important:** Uncommitted Changes တွေ ပျောက်သွားမယ်။

Staging Area ထဲက File ကို Unstage လုပ်ချင်ရင် -

```bash
git restore --staged app.js
```

ဒါက Staging Area ထဲက File ကို ဖယ်ပေးတာဖြစ်ပြီး Working Directory ထဲက Changes တွေကို မဖျက်ဘူး။

## 3.5 git reset

`git reset` က HEAD နဲ့ Current Branch Pointer ကို အရင် Commit တစ်ခုဆီ ပြန်ရွှေ့ဖို့ အသုံးပြုတယ်။

အဓိက Reset Modes သုံးမျိုးရှိတယ်။

### A. Soft Reset

```bash
git reset --soft HEAD~1
```

နောက်ဆုံး Commit ကို ပြန်ဖြုတ်မယ်။

ဒါပေမဲ့ Changes တွေက Staging Area ထဲမှာ ဆက်ရှိနေမယ်။

```text
Before:

A --- B --- C (HEAD)

After:

A --- B (HEAD)

C ရဲ့ Changes = Staged
```

**Use case:**

Commit လုပ်ပြီးမှ Commit Message မှားနေတယ်ဆိုရင် -

```bash
git reset --soft HEAD~1
git commit -m "correct commit message"
```

### B. Mixed Reset

```bash
git reset --mixed HEAD~1
```

ဒါက Default Reset Mode ဖြစ်တယ်။

နောက်ဆုံး Commit ကို ပြန်ဖြုတ်မယ်။

Changes တွေကို Staging Area ကနေ ဖယ်ပြီး Working Directory ထဲမှာ ထားပေးမယ်။

```text
Before:

A --- B --- C (HEAD)

After:

A --- B (HEAD)

C ရဲ့ Changes = Unstaged
```

**Use case:**

Commit ကို ပြန်ဖြုတ်ချင်ပေမဲ့ Code Changes တွေကို ပြန်ပြင်ချင်သေးတဲ့အခါ။

```bash
git reset HEAD~1
```

ဒီ Command က Mixed Reset နဲ့ အတူတူပဲ။

### C. Hard Reset

```bash
git reset --hard HEAD~1
```

နောက်ဆုံး Commit ကို ပြန်ဖြုတ်မယ်။

Staging Area နဲ့ Working Directory ထဲက Changes တွေကိုလည်း Reset လုပ်မယ်။

```text
Before:

A --- B --- C (HEAD)

After:

A --- B (HEAD)
```

**Warning:**

Uncommitted Changes တွေ ပျောက်သွားနိုင်တယ်။

Hard Reset ကို သတိထားပြီး အသုံးပြုပါ။

## 3.6 HEAD~1, HEAD~2, HEAD^ Explained

`HEAD` ဆိုတာ Current Commit ကို ရည်ညွှန်းတယ်။

| Reference | Meaning                       |
| --------- | ----------------------------- |
| `HEAD`    | Current Commit                |
| `HEAD~1`  | First Parent Commit           |
| `HEAD~2`  | Two First-Parent Steps Back   |
| `HEAD~3`  | Three First-Parent Steps Back |
| `HEAD^`   | First Parent Commit           |
| `HEAD^2`  | Second Parent Commit          |

Example:

```text
A --- B --- C --- D (HEAD)
```

```bash
git reset --soft HEAD~1
```

D ကနေ C ကို ပြန်သွားမယ်။

```bash
git reset --soft HEAD~2
```

D ကနေ B ကို ပြန်သွားမယ်။

## 3.7 git revert

`git revert` က အရင် Commit တစ်ခုရဲ့ Changes တွေကို ပြန်ဖျက်ပေးမယ့် Commit အသစ်တစ်ခု ဖန်တီးပေးတယ်။

ဥပမာ -

```text
A --- B --- C --- D (HEAD)
```

C ရဲ့ Changes တွေကို မလိုချင်တော့ဘူး။

```bash
git revert C
```

After:

```text
A --- B --- C --- D --- R (HEAD)
```

`R` က C ရဲ့ Changes တွေကို ပြန်လည်ပယ်ဖျက်ပေးတဲ့ Commit ဖြစ်တယ်။

**Important:**

`git revert` က အရင် Commit ကို History ထဲက မဖျက်ဘူး။

Commit အသစ်ဖန်တီးပြီး Changes ကို ပြန်ဖြုတ်ပေးတာ ဖြစ်တယ်။

**When to use:**
- Shared Branch.
- GitHub Main Branch.
- Production Code.
- Already Pushed Commits.

## 3.8 git reflog

**Reflog** ဆိုတာ HEAD နဲ့ Branch References တွေ အရင်က ဘယ်နေရာတွေကို ရွှေ့ခဲ့လဲဆိုတာ မှတ်တမ်းတင်ပေးတာဖြစ်တယ်။

ဥပမာ -

မတော်တဆ Hard Reset လုပ်လိုက်တယ်။

```bash
git reset --hard HEAD~2
```

အရင် Commit တွေကို ပြန်လိုချင်တယ်။

ဒီလိုအချိန်မှာ -

```bash
git reflog
```

အသုံးပြုနိုင်တယ်။

Example output:

```text
a1b2c3d HEAD@{0}: reset: moving to HEAD~2
e4f5g6h HEAD@{1}: commit: add payment feature
i7j8k9l HEAD@{2}: commit: add login feature
```

အရင် Commit ကို ပြန်ရယူချင်ရင် -

```bash
git reset --hard e4f5g6h
```

သို့မဟုတ် အရင် Commit ကနေ Branch အသစ် ဖန်တီးနိုင်တယ်။

```bash
git switch -c recovery-branch e4f5g6h
```

**Important:**

Reflog က Local Repository မှာပဲ အလုပ်လုပ်တယ်။ အချိန်အကန့်အသတ်နဲ့ သိမ်းထားတာဖြစ်လို့ အမြဲတမ်း ရှိနေမယ်လို့ မယူဆပါနဲ့။

## 3.9 Time Travel Command Comparison

| Command                     | What it does                 | Keeps Changes?                |
| --------------------------- | ---------------------------- | ----------------------------- |
| `git checkout <hash>`       | Visit old commit             | Yes, but detached HEAD        |
| `git switch <branch>`       | Switch branch                | Yes                           |
| `git restore file`          | Discard file changes         | No                            |
| `git restore --staged file` | Unstage file                 | Yes                           |
| `git reset --soft`          | Move HEAD backward           | Yes, staged                   |
| `git reset --mixed`         | Move HEAD backward           | Yes, unstaged                 |
| `git reset --hard`          | Move HEAD backward           | No, working changes discarded |
| `git revert`                | Undo a commit                | Yes, through a new commit     |
| `git reflog`                | Find previous HEAD positions | Helps recover references      |

---

# 4. Git Rebase vs Merge

## 4.1 What is Git Rebase?

**Git Rebase** ဆိုတာ Current Branch ရဲ့ Commits တွေကို Base Commit အသစ်တစ်ခုပေါ်မှာ ပြန်တည်ဆောက်ပေးတာ ဖြစ်တယ်။

လွယ်လွယ်ပြောရရင် ကိုယ့် Feature Branch ကို Main Branch ရဲ့ Latest Commit နောက်မှာ ပြန်တင်ပေးတာ ဖြစ်တယ်။

ဥပမာ -

Initial:

```text
A --- B --- C (main)
            \
             D --- E (feature)
```

Meanwhile, Main မှာ Commit အသစ် ထပ်ဝင်လာတယ်။

```text
A --- B --- C --- F --- G (main)
            \
             D --- E (feature)
```

Feature Branch က Main ရဲ့ Latest Changes တွေ မပါသေးဘူး။

Rebase လုပ်မယ်။

```bash
git switch feature
git rebase main
```

After Rebase:

```text
A --- B --- C --- F --- G (main)
                         \
                          D' --- E' (feature)
```

`D'` နဲ့ `E'` က Original Commits တွေကို Base အသစ်ပေါ်မှာ ပြန်ဖန်တီးထားတဲ့ Commits အသစ်တွေ ဖြစ်တယ်။

**Important:**

Rebase လုပ်တဲ့အခါ Commit Hash တွေ ပြောင်းသွားတယ်။

ဘာကြောင့်လဲ?

Commit Hash က Commit Content, Parent Commit, Metadata စတာတွေပေါ် မူတည်ပြီး ဖန်တီးထားတာဖြစ်တယ်။

Parent Commit ပြောင်းသွားတဲ့အတွက် Commit Hash လည်း ပြောင်းသွားတယ်။

## 4.2 What is Git Merge?

Merge က Branch နှစ်ခုရဲ့ History ကို ပေါင်းစည်းတာ ဖြစ်တယ်။

Before:

```text
A --- B --- C --- F --- G (main)
            \
             D --- E (feature)
```

Merge:

```bash
git switch main
git merge feature
```

After:

```text
A --- B --- C --- F --- G --- M (main)
            \               /
             D -------- E --
```

Merge က Original Commits တွေကို မပြောင်းဘူး။

Merge Commit အသစ်တစ်ခု ဖန်တီးပေးတယ် (Three-way merge ဖြစ်တဲ့အခါ)။

## 4.3 Rebase vs Merge Comparison

| Topic         | Rebase                | Merge                            |
| ------------- | --------------------- | -------------------------------- |
| History       | Linear History ဖြစ်စေတယ်  | Branch History ကို ထိန်းထားတယ်        |
| New Commit    | Commits အသစ်တွေ ပြန်ဖန်တီးတယ် | Merge Commit ဖန်တီးနိုင်တယ်            |
| Commit Hash   | Changed               | Original commits unchanged       |
| History       | ရှင်းလင်းတယ်              | Branch structure ကို မြင်နိုင်တယ်        |
| Conflict      | Rebase လုပ်စဉ် ဖြေရှင်းရတယ်   | Merge လုပ်စဉ် ဖြေရှင်းရတယ်               |
| Shared Branch | သတိထားရမယ်              | ပိုပြီး Safe ဖြစ်တယ်                    |
| Use Case      | Local Feature Branch  | Shared Branch / Team Integration |

## 4.4 Rebase Command Examples

Update Feature Branch with Main:

```bash
git switch feature/login
git fetch origin
git rebase origin/main
```

Rebase ပြီးရင် Feature Branch ကို ဆက်လုပ်နိုင်တယ်။

```bash
git add .
git commit -m "feat: improve login validation"
```

Rebase Conflict ဖြစ်ရင် -

**Step 1: Check Conflict**

```bash
git status
```

**Step 2: Open Conflicted Files**

Conflict Example:

```text
<<<<<<< HEAD
Main branch code
=======
Feature branch code
>>>>>>> feature commit
```

လိုအပ်တဲ့ Code ကို ရွေးပြီး Conflict Markers တွေကို ဖယ်ရှားမယ်။

**Step 3: Mark as Resolved**

```bash
git add .
```

**Step 4: Continue Rebase**

```bash
git rebase --continue
```

Rebase ကို Cancel လုပ်ချင်ရင် -

```bash
git rebase --abort
```

ဒီ Command က Rebase မစခင် အခြေအနေကို ပြန်သွားပေးတယ်။

## 4.5 Interactive Rebase

Interactive Rebase ကို Commit History ကို ပြင်ဆင်ဖို့ အသုံးပြုတယ်။

ဥပမာ -

```text
A --- B --- C --- D --- E
```

နောက်ဆုံး Commit သုံးခုကို ပြင်ချင်တယ်။

```bash
git rebase -i HEAD~3
```

Editor ပွင့်လာမယ်။

```text
pick abc1234 add login form
pick def5678 fix login button
pick ghi9012 update login validation
```

အသုံးများတဲ့ Commands:

| Command  | Meaning                                                   |
| -------- | --------------------------------------------------------- |
| `pick`   | Keep Commit                                               |
| `reword` | Change Commit Message                                     |
| `edit`   | Stop and Edit Commit                                      |
| `squash` | Combine with Previous Commit                              |
| `fixup`  | Combine with Previous Commit, discard this commit message |
| `drop`   | Remove Commit                                             |

Example:

```text
pick abc1234 add login form
squash def5678 fix login button
squash ghi9012 update login validation
```

ဒီလိုဆို Commit သုံးခုကို တစ်ခုတည်းအဖြစ် ပေါင်းနိုင်တယ်။

**Warning:**

Interactive Rebase လုပ်တဲ့အခါ Commit Hash တွေ ပြောင်းသွားတယ်။ Shared Branch တွေမှာ သတိထားပါ။

## 4.6 When Should You Use Rebase?

Rebase ကို အသုံးပြုသင့်တဲ့အချိန် -

- ကိုယ်ပိုင် Feature Branch မှာ အလုပ်လုပ်နေတဲ့အခါ။
- Main Branch ရဲ့ Latest Changes တွေ ယူချင်တဲ့အခါ။
- Commit History ကို Linear ဖြစ်စေချင်တဲ့အခါ။
- Commit အများကြီးကို Clean Up လုပ်ချင်တဲ့အခါ။

Example:

```bash
git switch feature/login
git fetch origin
git rebase origin/main
```

## 4.7 When Should You Use Merge?

Merge ကို အသုံးပြုသင့်တဲ့အချိန် -

- Feature Branch ကို Main ထဲ ပေါင်းချင်တဲ့အခါ။
- Team Shared Branch တွေမှာ အလုပ်လုပ်တဲ့အခါ။
- Original Commit History ကို ထိန်းထားချင်တဲ့အခါ။
- Public History ကို မပြောင်းချင်တဲ့အခါ။

Example:

```bash
git switch main
git merge feature/login
```

## 4.8 Golden Rule of Rebase

**Never Rebase Public / Shared Commits that other developers may already depend on, unless your team explicitly coordinates the history rewrite.**

အဓိပ္ပာယ်က -

ကိုယ်တစ်ယောက်တည်း အလုပ်လုပ်နေတဲ့ Local Feature Branch ကို Rebase လုပ်တာ အဆင်ပြေတယ်။

ဒါပေမဲ့ တခြား Developer တွေ အသုံးပြုနေတဲ့ Shared Branch ကို Rebase လုပ်ပြီး History ပြောင်းပစ်တာမျိုး မလုပ်သင့်ဘူး။

ဘာကြောင့်လဲ?

Developer A:

```text
A --- B --- C (shared)
```

Developer B က `C` ကို အခြေခံပြီး အလုပ်လုပ်နေတယ်။

Developer A က Rebase လုပ်လိုက်တယ်။

```text
A --- B --- C' (shared)
```

အခု Developer B ရဲ့ Local History က အရင် `C` ကို ဆက်ညွှန်နေတယ်။

ဒါကြောင့် History Divergence ဖြစ်ပြီး Push/Pull လုပ်တဲ့အခါ ပြဿနာတွေ ဖြစ်နိုင်တယ်။

---

# 5. Common Git Mistakes

## 5.1 Accidentally Committed to the Wrong Branch

မတော်တဆ `main` မှာ Commit လုပ်မိတယ်။

မPush ရသေးဘူးဆိုရင် -

```bash
git branch feature/new-feature
git reset --hard HEAD~1
git switch feature/new-feature
```

ဒီနည်းလမ်းမှာ Commit ကို Feature Branch မှာ ထိန်းထားပြီး Main ကို ပြန်ရွှေ့ပေးတယ်။

**Warning:** `git reset --hard` က Uncommitted Changes တွေကို ဖျက်နိုင်တယ်။ လုပ်ဆောင်မှုမပြုခင် `git status` စစ်ပါ။

## 5.2 Change the Last Commit Message

```bash
git commit --amend -m "new commit message"
```

ဒီ Command က နောက်ဆုံး Commit ကို ပြန်ဖန်တီးတယ်။

Push လုပ်ပြီးသား Commit ဖြစ်ရင် Shared History ကို ပြောင်းသွားနိုင်လို့ သတိထားပါ။

## 5.3 Add Forgotten Files to Last Commit

```bash
git add forgotten-file.js
git commit --amend --no-edit
```

နောက်ဆုံး Commit ထဲကို File အသစ် ထည့်ပေးတယ်။

## 5.4 Undo Last Commit but Keep Changes

```bash
git reset --soft HEAD~1
```

Changes တွေကို Staging Area ထဲမှာ ထားပေးတယ်။

## 5.5 Undo Last Commit and Unstage Changes

```bash
git reset HEAD~1
```

Changes တွေကို Working Directory ထဲမှာ ထားပေးတယ်။

## 5.6 Undo a Pushed Commit

```bash
git revert <commit-hash>
git push
```

Shared Branch မှာ ဒီနည်းလမ်းကို ပိုပြီး အကြံပြုတယ်။

## 5.7 Recover Lost Commit

```bash
git reflog
```

Commit Hash ကို ရှာပြီး -

```bash
git switch -c recovery-branch <commit-hash>
```

## 5.8 Stash Changes

Commit မလုပ်ချင်သေးပေမဲ့ Branch ပြောင်းချင်တဲ့အခါ -

```bash
git stash
```

Stash List:

```bash
git stash list
```

Apply Latest Stash:

```bash
git stash apply
```

Apply and Remove Latest Stash:

```bash
git stash pop
```

Stash ကို Delete လုပ်မယ်။

```bash
git stash drop
```

All Stashes ကို Delete လုပ်မယ်။

```bash
git stash clear
```

သတိထားပါ။ `stash clear` က Stash အားလုံးကို ဖျက်ပစ်တယ်။

---

# 6. Quick Command Reference

## Branch Commands

```bash
git branch                    # List local branches
git branch -a                 # List all branches
git branch feature/login      # Create branch
git switch main               # Switch branch
git switch -c feature/login   # Create and switch
git branch -m new-name        # Rename current branch
git branch -d feature/login   # Safe delete
git branch -D feature/login   # Force delete
```

## Merge Commands

```bash
git merge feature/login
git merge --ff feature/login
git merge --no-ff feature/login
git merge --ff-only feature/login
git merge --squash feature/login
```

## Rebase Commands

```bash
git rebase main
git rebase -i HEAD~3
git rebase --continue
git rebase --abort
git rebase --skip
```

## Time Travel Commands

```bash
git log --oneline
git log --oneline --graph --all
git checkout <commit-hash>
git restore app.js
git restore --staged app.js
git reset --soft HEAD~1
git reset --mixed HEAD~1
git reset --hard HEAD~1
git revert <commit-hash>
git reflog
```

## Remote Commands

```bash
git remote -v
git fetch origin
git pull origin main
git push origin main
git push -u origin feature/login
git push origin --delete feature/login
git fetch --prune
```

## Stash Commands

```bash
git stash
git stash list
git stash apply
git stash pop
git stash drop
git stash clear
```

---

# 7. Final Mental Model

ဒီအချက်တွေကို နားလည်ထားရင် Git ကို ပိုပြီး လွယ်လွယ်ကူကူ အသုံးပြုနိုင်မယ်။

| Concept         | Remember This                            |
| --------------- | ---------------------------------------- |
| Branch          | သီးခြားအလုပ်လုပ်ဖို့ Branch ခွဲတာ                   |
| Merge           | Branch နှစ်ခုရဲ့ Changes တွေ ပေါင်းတာ             |
| Fast-forward    | Branch Pointer ကို ရှေ့ရွှေ့တာ                    |
| Three-way Merge | Common Ancestor ကို အသုံးပြုပြီး Merge လုပ်တာ      |
| Squash          | Commit အများကြီးကို တစ်ခုတည်းအဖြစ် ပေါင်းတာ          |
| Rebase          | Commits တွေကို Base အသစ်ပေါ် ပြန်တည်ဆောက်တာ          |
| Reset           | Branch/HEAD ကို အရင် Commit ဆီ ပြန်ရွှေ့တာ         |
| Revert          | Commit အသစ်ဖန်တီးပြီး အရင် Changes ကို ပြန်ဖျက်တာ    |
| Restore         | File Changes တွေကို ပြန်ပြင်တာ                   |
| Reflog          | HEAD နဲ့ Branch Reference History ကို ပြန်ရှာတာ |
| Stash           | Commit မလုပ်ရသေးတဲ့ Changes တွေကို ခဏသိမ်းတာ        |

## Most Important Rules

1. `git reset --hard` ကို သတိထားပြီး အသုံးပြုပါ။
2. Shared Branch တွေကို Rebase မလုပ်ပါနဲ့၊ Team က ခွင့်ပြုထားပြီး ညှိနှိုင်းထားတဲ့အခြေအနေ မဟုတ်ရင်။
3. Pushed Commit ကို Undo လုပ်ချင်ရင် `git revert` ကို စဉ်းစားပါ။
4. Local Feature Branch မှာ Rebase လုပ်တာ ပုံမှန်အားဖြင့် အဆင်ပြေတယ်။
5. Commit မလုပ်ခင် `git status` ကို စစ်ဆေးပါ။
6. Merge မလုပ်ခင် Target Branch ရဲ့ Latest Changes တွေကို Update လုပ်ပါ။
7. အရေးကြီးတဲ့ Changes တွေ မလုပ်ခင် Backup Branch ဖန်တီးထားပါ။

---

# Recommended Official Resources

- Git Official Documentation: https://git-scm.com/doc
- Git Book: https://git-scm.com/book/en/v2
- Git Branching Interactive Tutorial: https://learngitbranching.js.org/
- GitHub Git Handbook: https://docs.github.com/en/get-started/using-git/about-git

---

**End of Git Complete Cheat Sheet**

Happy Coding! 🚀