# Multiple GitHub Accounts on one laptop

- Use multiple GitHub accounts on one laptop without conflicts:
  - Personal → default everywhere
  - Company → only inside a specific folder (`~/github.com/company-username/`)
- This setup ensures:
  - Personal account is used unless you’re inside the company folder
  - Company account is auto-selected for repos under `~/github.com/company-username/`
  - No accidental pushes from the wrong account
  - No need to type `git@github-company`: when cloning in the company folder

## Step-by-Step Setup

1. Create an SSH key for the company account

```bash
ssh-keygen -t ed25519 -C "your.name@company.com" -f ~/.ssh/id_ed25519_company
```

(If your personal key is RSA or ed25519 already, no change needed.)

2. Add keys to the SSH agent

```bash
eval "$(ssh-agent -s)"
ssh-add --apple-use-keychain ~/.ssh/id_rsa                 # personal (if applicable)
ssh-add --apple-use-keychain ~/.ssh/id_ed25519_company     # company
```

3. Add public keys to GitHub

```bash
pbcopy < ~/.ssh/id_rsa.pub                # personal (or ~/.ssh/id_ed25519.pub)
pbcopy < ~/.ssh/id_ed25519_company.pub    # company
```

- Personal key → GitHub (personal) ➜ Settings → SSH and GPG keys
- Company key → GitHub (company) ➜ Settings → SSH and GPG keys

4. SSH config with aliases
   Edit `~/.ssh/config`:

```bash
# Personal GitHub (default)
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa      # or ~/.ssh/id_ed25519 if that's your personal key
  AddKeysToAgent yes
  UseKeychain yes
  IdentitiesOnly yes

# Company GitHub
Host github-company
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_company
  AddKeysToAgent yes
  UseKeychain yes
  IdentitiesOnly yes
```

> IdentitiesOnly yes is critical—SSH will only offer the specified key for each host.

5. Global Git config (personal as default)
   Edit `~/.gitconfig`:

```bash
[user]
  name = Your Personal Name
  email = personal@example.com

[pull]
  ff = true

# Rewrite HTTPS GitHub URLs to SSH for personal usage
[url "git@github.com:"]
  insteadOf = https://github.com/

# Load company overrides only inside the company folder
[includeIf "gitdir:~/github.com/company-username/**"]
  path = ~/.gitconfig-company
```

> Note the /\*\* to match all repos under that folder.

6. Company-specific Git config
   Create `~/.gitconfig-company`:

```bash
[user]
  name = Your Company Name
  email = your.name@company.com

# Force company SSH alias inside the company folder
[url "git@github-company:"]
  insteadOf = git@github.com:
[url "git@github-company:"]
  insteadOf = https://github.com/
```

Inside `~/github.com/company-username/\*\*`, any `git@github.com:` or `https://github.com/` URL is auto-rewritten to `git@github-company:` (which uses the company key).

7. Cloning repos

- Personal profile is default
- Company repo (normal URL works; it will be rewritten)

```bash
cd ~/github.com/company-username/
git clone git@github.com:company-username/my-repo.git # this becomes git@github-company:company-username/my-repo.git
```

🔍 Verifying the Setup

Inside a personal repo

```bash
git config user.email   # -> personal@example.com
git remote -v           # -> git@github.com:personal-username/...
```

Inside a company repo

```bash
git config user.email   # -> your.name@company.com
git remote -v           # -> git@github-company:company-username/...
```

See which key SSH offers

```bash
GIT_SSH_COMMAND='ssh -v' git ls-remote origin
```

Look for:

```bash
Offering public key: /Users/you/.ssh/id_rsa                # personal repo
Offering public key: /Users/you/.ssh/id_ed25519_company    # company repo
```

## How It Works

- **Global config** sets personal identity and HTTPS→SSH rewrite
- **includeIf** activates company overrides only under `~/github.com/company-username/\*\*`
- **Company config** rewrites remotes to `git@github-company:…`
- **SSH aliases + IdentitiesOnly** ensure the correct key is used per host
