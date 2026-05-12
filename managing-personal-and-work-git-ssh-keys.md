# Managing Personal & Work Git SSH Keys — The Dragon Ball Z Edition

> *"Just like a Saiyan warrior needs to control their power levels,
> a developer needs to control which SSH key fires for which repo."*

---

## The Problem: One Key Ring, Two Saiyan Forms

Most of us juggle two GitHub accounts: one for **work** (company Org)
and one for **personal** (side projects, open source). The problem?
Git's SSH setup defaults to **one identity at a time** — like Goku
trying to fight as both Super Saiyan and Great Ape simultaneously.

```
╔══════════════════════════════════════════════════════════╗
║  YOUR CURRENT SETUP (before this guide)                  ║
╠══════════════════════════════════════════════════════════╣
║                                                          ║
║   GitHub.com                                             ║
║   ┌─────────────────────┐                                ║
║   │  Which key do I use? │                                ║
║   └─────────┬───────────┘                                ║
║             │                                             ║
║             ▼                                             ║
║   ┌─────────────────────┐                                ║
║   │  ~/.ssh/id_ed25519  │  ← THE DEFAULT KEY             ║
║   │  (work key)         │     Always picked first!        ║
║   └─────────────────────┘                                ║
║                                                          ║
║   Result: push to personal repo → 🔴 PERMISSION DENIED   ║
║                                                          ║
╚══════════════════════════════════════════════════════════╝
```

Git tries every key in `~/.ssh/` until one works. If your **work key**
authenticates first (because it's listed first in SSH config or was
added to the agent), GitHub rejects the push to your personal repo.

It's like sending Vegeta to fight in a tournament where only Goku
is registered. Wrong warrior, wrong arena.

---

## The Solution: SSH Config — The Fusion Dance

The fix is beautifully simple: **`~/.ssh/config`**. This file is like
the fusion dance — it tells SSH exactly which identity (key) to use
for which host (GitHub account).

```
╔══════════════════════════════════════════════════════════╗
║  THE FUSION DANCE: SSH CONFIG                            ║
╠══════════════════════════════════════════════════════════╣
║                                                          ║
║   git@github.com                → WORK KEY (default)     ║
║   git@github-personal           → PERSONAL KEY           ║
║                                                          ║
║   Two fighters. Two power levels. One config file.       ║
║                                                          ║
╚══════════════════════════════════════════════════════════╝
```

### How It Works (Step by Step)

#### Step 1: Generate Two Separate Keys

```bash
# Work key (your company provides this or you generate it)
ssh-keygen -t ed25519 -C "you@company.com" -f ~/.ssh/id_ed25519_work

# Personal key
ssh-keygen -t ed25519 -C "you@users.noreply.github.com" -f ~/.ssh/id_ed25519_personal
```

Each key is a separate warrior. Think of them as:
```
    ┌──────────────────────────┐
    │  id_ed25519_work         │  ← Vegeta (work mode)
    │  "Over 9000 PRs!"       │
    └──────────────────────────┘

    ┌──────────────────────────┐
    │  id_ed25519_personal     │  ← Goku (personal mode)
    │  "Kamehameha commit!"    │
    └──────────────────────────┘
```

#### Step 2: Add Both Public Keys to GitHub

Go to each GitHub account → Settings → SSH and GPG keys → New SSH key:

| Account | Public Key File |
|---------|----------------|
| Work GitHub | `~/.ssh/id_ed25519_work.pub` |
| Personal GitHub | `~/.ssh/id_ed25519_personal.pub` |

#### Step 3: Configure `~/.ssh/config`

This is the **fusion dance** — the magic that makes it all work:

```
# ═══════════════════════════════════════════════════
#  ~/.ssh/config
# ═══════════════════════════════════════════════════

# --- DEFAULT: Work account ---
Host *
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519_work

# --- PERSONAL: Override for personal repos ---
Host github-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal
  IdentitiesOnly yes
```

```
╔══════════════════════════════════════════════════════════╗
║  HOW SSH CONFIG MATCHING WORKS                          ║
╠══════════════════════════════════════════════════════════╣
║                                                          ║
║  git remote set-url origin \                             ║
║    git@github-personal:user/repo.git                     ║
║                 │                                        ║
║                 ▼                                        ║
║  SSH checks config: "Does 'github-personal' match?"     ║
║                 │                                        ║
║         ┌───────┴────────┐                               ║
║         │ YES → Use the  │                               ║
║         │ specific key!  │                               ║
║         └────────────────┘                               ║
║                 │                                        ║
║                 ▼                                        ║
║  IdentityFile = ~/.ssh/id_ed25519_personal  ✅          ║
║  IdentitiesOnly = yes  (ignore all other keys)          ║
║                                                          ║
║  Real hostname resolves to: github.com                   ║
║  But SSH uses YOUR chosen key. Fusion complete.          ║
║                                                          ║
╚══════════════════════════════════════════════════════════╝
```

The key insight: **`Host github-personal`** is a **virtual hostname**.
It doesn't exist in DNS. SSH intercepts it, reads the config block,
and connects to the real `github.com` using your personal key.

#### Step 4: Use Different Remote URLs Per Repo

This is where the rubber meets the road:

```bash
# For work repos (uses default / work key)
git remote add origin git@github.com:company/project.git

# For personal repos (uses personal key)
git remote add origin git@github-personal:yourname/project.git
```

```
╔══════════════════════════════════════════════════════════╗
║  THE TWO REALITIES                                       ║
╠══════════════════════════════════════════════════════════╣
║                                                          ║
║  WORK REPO                        PERSONAL REPO         ║
║  ┌────────────────────┐           ┌──────────────────┐   ║
║  │ git@github.com:    │           │ git@github-      │   ║
║  │   company/repo     │           │  personal:user/  │   ║
║  │        │           │           │    repo          │   ║
║  │        ▼           │           │      │           │   ║
║  │  id_ed25519_work   │           │      ▼           │   ║
║  │  (Vegeta)    ✅    │           │  id_ed25519_     │   ║
║  │                    │           │  personal (Goku)  │   ║
║  │  Push to company/  │           │  ✅              │   ║
║  │  project           │           │  Push to user/   │   ║
║  └────────────────────┘           │  project         │   ║
║                                   └──────────────────┘   ║
║                                                          ║
╚══════════════════════════════════════════════════════════╝
```

---

## Git Config: Two Emails, Two Identities

SSH keys handle **authentication** (who are you?). But git also needs
to know your **identity for commits** (what name/email appears?).

### The Problem

Your global `~/.gitconfig` probably looks like this:

```
[user]
  name = Your Name
  email = you@company.com    ← Work email everywhere!
```

Every commit on every repo uses your work email. Including your
personal side projects. Awkward.

### The Solution: Local Git Config

Set the global config for work (it's already there), and override
per-repo for personal:

```bash
# In your personal repo:
git config --local user.name "your-github-username"
git config --local user.email "yourname@users.noreply.github.com"
```

```
╔══════════════════════════════════════════════════════════╗
║  GIT CONFIG PRIORITY (highest wins)                      ║
╠══════════════════════════════════════════════════════════╣
║                                                          ║
║  Priority     File               Scope                  ║
║  ─────────────────────────────────────────────           ║
║  1 (highest)  .git/config        ← This repo only       ║
║  2            ~/.gitconfig       ← All repos (global)   ║
║  3 (lowest)   /etc/gitconfig     ← System-wide          ║
║                                                          ║
║  ┌─────────────────────────────────────────────┐        ║
║  │  Personal repo reads .git/config FIRST      │        ║
║  │  → Finds personal email → USES IT ✅        │        ║
║  │  → Never reaches ~/.gitconfig              │        ║
║  └─────────────────────────────────────────────┘        ║
║                                                          ║
║  ┌─────────────────────────────────────────────┐        ║
║  │  Work repo has NO .git/config override      │        ║
║  │  → Falls through to ~/.gitconfig            │        ║
║  │  → Uses work email ✅                       │        ║
║  └─────────────────────────────────────────────┘        ║
║                                                          ║
╚══════════════════════════════════════════════════════════╝
```

**Pro tip:** You can also use conditional includes for directory-based
config (no need to set it per-repo):

```
# ~/.gitconfig
[includeIf "gitdir:~/Documents/personal/"]
  path = ~/.gitconfig-personal

# ~/.gitconfig-personal
[user]
  name = your-github-username
  email = yourname@users.noreply.github.com
```

This auto-applies personal config to any repo under `~/Documents/personal/`.

---

## GitHub CLI: Switching Accounts

If you use `gh` (GitHub CLI), you likely have two accounts:

```bash
$ gh auth status

github.com
  ✓ Logged in to github.com account yourname (keyring)
  - Active account: true

  ✓ Logged in to github.com account work-user (keyring)
  - Active account: false
```

Switch between them:

```bash
# Switch to personal
gh auth switch --user yourname

# Switch to work
gh auth switch --user work-user
```

```
╔══════════════════════════════════════════════════════════╗
║  GH CLI SWITCHING                                        ║
╠══════════════════════════════════════════════════════════╣
║                                                          ║
║   ┌──────────────┐    switch     ┌──────────────┐       ║
║   │  WORK USER   │ ──────────→  │  PERSONAL    │       ║
║   │  (active)    │              │  USER        │       ║
║   └──────────────┘              │  (active)    │       ║
║        ▲                        └──────────────┘       ║
║        │                              │                 ║
║        └──────────────────────────────┘                 ║
║              gh auth switch --user <name>               ║
║                                                          ║
║  Like switching between Goku and Vegeta                 ║
║  with the Potara earrings. But reversible.              ║
║                                                          ║
╚══════════════════════════════════════════════════════════╝
```

---

## The Complete Setup Checklist

```
╔══════════════════════════════════════════════════════════╗
║  SETUP CHECKLIST                                         ║
╠══════════════════════════════════════════════════════════╣
║                                                          ║
║  □ Generate separate SSH keys                            ║
║    ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_work      ║
║    ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_personal  ║
║                                                          ║
║  □ Add public keys to respective GitHub accounts         ║
║    Work key    → Settings → SSH Keys (work account)     ║
║    Personal key → Settings → SSH Keys (personal acct)   ║
║                                                          ║
║  □ Configure ~/.ssh/config                               ║
║    Host *          → default work key                    ║
║    Host github-personal → personal key + IdentitiesOnly  ║
║                                                          ║
║  □ Test both connections                                 ║
║    ssh -T git@github.com          → work account  ✅    ║
║    ssh -T git@github-personal     → personal acct ✅    ║
║                                                          ║
║  □ Set local git config for personal repos              ║
║    git config --local user.name "username"              ║
║    git config --local user.email "user@users.noreply"   ║
║                                                          ║
║  □ Use correct remote URL per repo                       ║
║    Work:    git@github.com:company/repo.git             ║
║    Personal: git@github-personal:user/repo.git          ║
║                                                          ║
║  □ Configure gh CLI for both accounts                    ║
║    gh auth login                                         ║
║    gh auth switch --user <name>                          ║
║                                                          ║
╚══════════════════════════════════════════════════════════╝
```

---

## Common Pitfalls

### Pitfall 1: "Permission denied (publickey)"
```
You're pushing to a personal repo but git@github.com
routes to your work key.

Fix: Change the remote URL to use github-personal:
  git remote set-url origin git@github-personal:user/repo.git
```

### Pitfall 2: Wrong email on commits
```
Your personal repo commits show your work email.

Fix: Set local config:
  git config --local user.email "you@users.noreply.github.com"
```

### Pitfall 3: SSH agent has too many keys
```
SSH tries keys in order and the wrong one authenticates.

Fix: Use IdentitiesOnly yes in your SSH config block.
This tells SSH to ONLY use the specified key, ignoring all others.
```

### Pitfall 4: gh CLI uses wrong account
```
gh repo create creates the repo under the wrong account.

Fix: Switch first:
  gh auth switch --user yourname
  gh repo create myrepo --private
```

---

## TL;DR

```
╔══════════════════════════════════════════════════════════╗
║  TL;DR — THE POWER LEVELS                                ║
╠══════════════════════════════════════════════════════════╣
║                                                          ║
║  1. TWO SSH KEYS                                         ║
║     One for work, one for personal. Never share.         ║
║                                                          ║
║  2. SSH CONFIG IS YOUR FUSION DANCE                      ║
║     Host github-personal → personal key                  ║
║     Host * (default)    → work key                       ║
║                                                          ║
║  3. USE VIRTUAL HOSTNAMES IN REMOTES                     ║
║     git@github-personal:user/repo.git                    ║
║     NOT git@github.com:user/repo.git                     ║
║                                                          ║
║  4. LOCAL GIT CONFIG FOR COMMIT IDENTITY                 ║
║     git config --local user.email "personal@email"       ║
║     Doesn't touch global config!                         ║
║                                                          ║
║  5. TEST WITH ssh -T                                     ║
║     ssh -T git@github.com        → "Hi work-user!"       ║
║     ssh -T git@github-personal   → "Hi personal-user!"   ║
║                                                          ║
║  6. GH CLI SWITCH FOR REPO CREATION                      ║
║     gh auth switch --user personal                       ║
║                                                          ║
╚══════════════════════════════════════════════════════════╝
```

That's it. Two warriors, two power levels, one config file.
Now go push some commits. 🐉

---

*Inspired by real-world SSH wrestling between work and personal GitHub accounts.*
