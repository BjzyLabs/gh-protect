# gh-protect

> GitHub CLI extension to apply BjzyLabs standard branch protection rules

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Overview

`gh-protect` is a GitHub CLI extension that applies consistent branch protection rules across all BjzyLabs repositories. It enforces GitFlow best practices while maintaining flexibility for solo developers.

## Features

- ✅ **Auto-detection**: Automatically detects current repository
- ✅ **Standard Protection**: Applies BjzyLabs branch protection rules
- ✅ **Solo Developer Friendly**: Allows self-merge without approval requirements
- ✅ **Emergency Override**: Admin bypass available on develop branch
- ✅ **Branch Creation**: Optionally creates develop branch if missing
- ✅ **Validation**: Checks prerequisites before applying protection

## Installation

```bash
gh extension install BjzyLabs/gh-protect
```

### Verify Installation

```bash
gh protect --version
```

## Usage

### Protect Current Repository

```bash
# From inside any git repository
cd ~/my-repo
gh protect
```

### Protect Specific Repository

```bash
gh protect BjzyLabs/ansible
gh protect BrianInAz/myContext
```

### Get Help

```bash
gh protect --help
```

## Branch Protection Rules

### Main Branch (Production)

```json
{
  "requires_pr": true,              ← All changes via PR
  "enforce_admins": true,           ← Even admins must use PRs
  "required_approving_reviews": 0,  ← Self-merge allowed
  "allow_force_pushes": false,      ← No force pushes
  "allow_deletions": false          ← Cannot delete branch
}
```

**What this means:**
- ❌ Cannot push directly to main
- ✅ Must use pull requests
- ✅ Can merge own PRs (no approval needed)
- ❌ Cannot force push
- ❌ Cannot delete branch

### Develop Branch (Integration)

```json
{
  "requires_pr": true,              ← All changes via PR
  "enforce_admins": false,          ← Admin bypass for emergencies
  "required_approving_reviews": 0,  ← Self-merge allowed
  "allow_force_pushes": false,      ← No force pushes
  "allow_deletions": false          ← Cannot delete branch
}
```

**What this means:**
- ⚠️ Cannot push directly to develop (but admin can override)
- ✅ Must use pull requests (normally)
- ✅ Can merge own PRs (no approval needed)
- ✅ Admin emergency override available
- ❌ Cannot force push
- ❌ Cannot delete branch

## Workflow

### Standard Development Workflow

```bash
# 1. Start from develop
git checkout develop
git pull origin develop

# 2. Create feature branch
git checkout -b feature/awesome-feature

# 3. Make changes and commit
git add .
git commit -m "feat: add awesome feature"

# 4. Push and create PR
git push origin feature/awesome-feature
gh pr create --base develop

# 5. Merge (no approval needed for solo dev)
gh pr merge --squash
```

### Emergency Override (Develop Only)

```bash
# When you absolutely need to bypass PR workflow
git checkout develop
git add .
git commit -m "hotfix: critical fix"
git push origin develop  # Admin bypass works
```

## Requirements

- [GitHub CLI](https://cli.github.com/) (`gh`) installed and authenticated
- Write access to target repository
- Repository must have a `main` branch
- Optionally: `develop` branch (will offer to create if missing)

## Examples

### Setup New Repository

```bash
# Create repo, then immediately apply protection
gh repo create BjzyLabs/new-project --public --clone
cd new-project
gh protect
```

### Audit Existing Repository

```bash
# Check current protection
gh api repos/BjzyLabs/ansible/branches/main/protection | jq

# Apply standard protection
cd ~/projects/ansible
gh protect
```

### Bulk Setup

```bash
# Apply to multiple repos
for repo in ansible mcp-servers ComplianceTracker; do
  gh protect BjzyLabs/$repo
done
```

## Troubleshooting

### "Not in a git repository"

Make sure you're inside a git repository or specify the repo explicitly:

```bash
gh protect BjzyLabs/repo-name
```

### "Not authenticated with GitHub"

```bash
gh auth login
```

### "Main branch not found"

The repository must have a `main` branch. If you use `master`, rename it first:

```bash
git branch -m master main
git push -u origin main
```

### "Failed to apply protection"

Check that you have admin/write access to the repository:

```bash
gh api repos/OWNER/REPO | jq '.permissions'
```

## Uninstall

```bash
gh extension remove protect
```

## Development

### Local Testing

```bash
# Clone and test locally
git clone https://github.com/BjzyLabs/gh-protect.git
cd gh-protect
./gh-protect --help

# Install local version
gh extension install .
```

### Update Extension

```bash
gh extension upgrade protect
```

## Documentation

- **Home Lab Documentation**: [Notion](https://www.notion.so/2af3569aa25581a6b07ed66a0170be14)
- **GitFlow Standards**: [02_GITFLOW.md](https://github.com/BrianInAz/myContext/blob/develop/.ruler/docs/standards/02_GITFLOW.md)
- **Context Management**: [myContext Repository](https://github.com/BrianInAz/myContext)

## Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/enhancement`)
3. Commit changes (`git commit -m 'feat: add enhancement'`)
4. Push to branch (`git push origin feature/enhancement`)
5. Create Pull Request targeting `develop`

## License

MIT License - see LICENSE file for details

## Support

- **Issues**: [GitHub Issues](https://github.com/BjzyLabs/gh-protect/issues)
- **Documentation**: [Notion Workspace](https://www.notion.so/26b3569aa25580169359fd9bd990430c)
- **Contact**: Bjzy Labs Infrastructure Team

---

**Made with ❤️ by Bjzy Labs**

