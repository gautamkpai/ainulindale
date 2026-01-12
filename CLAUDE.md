# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**ainulindale** (Ainulindalë - "The creation song") is an Ansible-based macOS system provisioning project that automates development environment setup on macOS machines.

## Commands

### Bootstrap (Full Setup)
```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/gautamkpai/ainulindale/master/bootstrap)"
```

### Run Individual Playbooks
```bash
ansible-playbook 0-test.yml        # Test Ansible connectivity
ansible-playbook 1-basics.yml      # Install basic GUI apps
ansible-playbook 2-dev.yml         # Install development tools
ansible-playbook 3-devops.yml      # Install DevOps/cloud tools
ansible-playbook 4-personalise.yml # System customization & dotfiles
```

### Post-Setup
```bash
rustup-init  # Complete Rust installation
```

## Architecture

The project follows a sequential playbook pattern:

```
bootstrap (Ruby) → 0-test.yml → 1-basics.yml → 2-dev.yml → 3-devops.yml → 4-personalise.yml
```

**Playbook responsibilities:**
- `bootstrap`: Installs Homebrew and Ansible, clones repo to `/tmp/ainulindale`
- `0-test.yml`: Validates Ansible connectivity (ping module)
- `1-basics.yml`: GUI apps (Firefox, Obsidian, Slack, Zoom, 1Password, VLC)
- `2-dev.yml`: Dev tools (git, vim, fzf, rg, bat), terminals (iTerm2, Kitty, Ghostty), language SDKs (rustup, go, pyenv, uv)
- `3-devops.yml`: Cloud tools (AWS CLI, kubectl, helm, Terraform, Docker), monitoring (k6, jq)
- `4-personalise.yml`: macOS settings, dotfiles symlinks from `~/src/pai/dotfiles/`, git config

**Key patterns:**
- All playbooks use `homebrew` and `homebrew_cask` modules for package installation
- Tasks use `state: present` for idempotency
- macOS-specific modules: `osx_defaults` for system preferences
- Inventory is localhost-only with local connection mode

## Adding New Tools

Add entries to the `vars` section of the appropriate playbook:

```yaml
vars:
  brew_packages:
    - new-cli-tool
  cask_packages:
    - new-gui-app
```

## Development Practices

- Follow conventional commits for git messages: `feat(scope): description`, e.g., `feat(dev): install claude-code`

## External Dependencies

- `4-personalise.yml` expects dotfiles repo at `git@github.com:gautamkpai/dotfiles.git`
- Symlinks created: `.vim`, `.vimrc`, `.gitignore_global`, `.zshrc`
