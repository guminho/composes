# Minimal Zsh Setup: Antidote + Starship + Zephyr

A high-performance, minimal shell configuration using Antidote for plugin management, Zephyr for core shell configurations, and Starship for the prompt.

---

## 🎯 Design Choices & Context

### Core Needs & Goals
* **High Performance**: Sub-20ms shell startup time.
* **Minimalism**: Clean `~/.zshrc` containing only the essentials (setup once and forget).
* **Two-Line Prompt**: Path on top, input on bottom to avoid line truncation in deep directories.
* **No Clutter**: No auto-polluting aliases or background processes that clobber standard Bash tools.

### Why Other Options Were Rejected
* **Oh-My-Zsh / Prezto**: Too heavy and slow, introducing too many default aliases.
* **Zinit / Zplug**: Overly complex configuration syntax, prone to breaking on updates.
* **Zap / Zcomet**: Inline plugin declarations clutter the main `~/.zshrc`.
* **Zimfw**: Very fast, but invasive; overrides native shell behaviors and defaults.
* **Pure Theme**: Sleek, but customizing it for full paths requires verbose configuration.

### The Selected Stack
1. **Antidote (Plugin Manager)**: Parses a simple `~/.zsh_plugins.txt` and compiles it into a static script. It is extremely fast (< 15ms) and runs zero background processes after startup.
2. **Zephyr (Micro-framework)**: Provides modular foundation components (history, editor, directory utilities) and initializes Zsh completions cleanly using built-in caching.
3. **Starship (Prompt)**: A fast, Rust-based prompt. By default, it uses a two-line layout and intelligently displays language/tool environments (Git, Kubernetes, AWS, UV, etc.) only when inside a relevant directory.

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

## ⚡ 3. Optimize Tool Completions (Zero Startup Cost)

Instead of using dynamic `eval` or generating completions on every shell startup, dump them statically to the user completions directory. Zephyr's completion plugin will scan this directory and lazy-load them on-demand.

First, ensure the directory exists:
```bash
mkdir -p ~/.config/zsh/completions
```

Then generate the static files:

*   **uv**:
    ```bash
    uv generate-shell-completion zsh > ~/.config/zsh/completions/_uv
    ```
*   **docker**:
    ```bash
    docker completion zsh > ~/.config/zsh/completions/_docker
    ```
*   **kubectl**:
    ```bash
    kubectl completion zsh > ~/.config/zsh/completions/_kubectl
    ```
