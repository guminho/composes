# Minimal Zsh Setup: Antidote + Starship + Zephyr

A high-performance, minimal shell configuration using Antidote for plugin management, Zephyr for core shell configurations, and Starship for the prompt.

---

## 🛠️ Prerequisites

Ensure these are installed before loading your shell configuration:
1. **Antidote**: Cloned to `~/.antidote` (or installed via Homebrew on macOS).
2. **Starship**: Installed to `~/.local/bin/starship` (or system-wide).
3. **fnm & uv**: Node and Python environment managers.

---

## 📂 1. Plugins configuration (`~/.zsh_plugins.txt`)

Declares the required plugins in the correct load order (completions last):

```text
# Zephyr Core
using:mattmc3/zephyr path:plugins
environment
editor
history
directory
utility

# Completions (adds to fpath)
zsh-users/zsh-completions

# UI/Interactive widgets
zsh-users/zsh-autosuggestions
zsh-users/zsh-syntax-highlighting

# Completion Engine Init (must run last)
using:mattmc3/zephyr path:plugins
completion
```

> ℹ️ **Note on Order:** In Zephyr's docs, `completion` is listed alphabetically for simplicity. However, when using third-party plugins (like `zsh-completions`), `completion` must be loaded last. This ensures `compinit` initializes only after all external completion paths have been added to Zsh's `$fpath`.

---

## ⚙️ 2. Shell Configuration (`~/.zshrc`)

```zsh
export PATH="$HOME/.local/bin:$HOME/.local/share/fnm:$PATH"

# Fnm (Fast Node Manager)
export FNM_PATH="$HOME/.local/share/fnm"
eval "$(fnm env --use-on-cd --shell zsh)"

# Antidote (Plugin Manager)
# NOTE: On macOS with Homebrew, change path to: $(brew --prefix)/share/antidote/antidote.zsh
source "$HOME/.antidote/antidote.zsh"
antidote load

# Starship (Prompt)
eval "$(starship init zsh)"
```

---

## ⚡ 3. Optimize UV Completions (Zero Startup Cost)

Instead of using `eval` on every startup, generate the completions statically:

```bash
mkdir -p ~/.config/zsh/completions
uv generate-shell-completion zsh > ~/.config/zsh/completions/_uv
```
Zephyr's completion plugin automatically scans `~/.config/zsh/completions` and loads `_uv` on-demand when you press `Tab`.
