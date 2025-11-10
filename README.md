# C4 Viewer

A minimal **C4 architecture diagram viewer** powered by [LikeC4](https://likec4.dev).  
This setup uses the **LikeC4 CLI only** (no React, no Vite) to **generate, export, and preview** interactive architecture diagrams — ideal for CI/CD and static hosting (e.g. **GitHub Pages**).

---

## Features

- ⚡ **CLI-based workflow** — no frontend build stack required  
- 🧠 **Graphviz** or **WASM** layout engines  
- 💾 **Static export** ready for Pages or any web host  
- 🧩 **JSON export** for automation, dashboards, or analysis  
- ☁️ **CI-ready** with `--use-dot` and GitHub Actions support  

---

## Installation

Install dependencies:

```bash
npm install
````

Then install **Graphviz**, required for the high-performance `--use-dot` mode.

### Install Graphviz

**macOS**

```bash
brew install graphviz
```

**Ubuntu / Debian**

```bash
sudo apt update && sudo apt install -y graphviz
```

**Fedora / RHEL**

```bash
sudo dnf install -y graphviz
```

**Alpine (for CI containers)**

```bash
apk add --no-cache graphviz
```

Verify installation:

```bash
dot -V
```

---

## ⚙️ WASM vs Binary Layout

| Mode                     | Description                                   | Pros                         | Cons                                                  |
| ------------------------ | --------------------------------------------- | ---------------------------- | ----------------------------------------------------- |
| **WASM (default)**       | Uses Graphviz → WebAssembly built into LikeC4 | ✅ Zero setup · Runs anywhere | ❌ Slower · Heavier memory · May hang on complex views |
| **Binary (`--use-dot`)** | Uses your system Graphviz binary (`dot`)      | ✅ Fast · Stable · Low memory | ❌ Requires Graphviz installed                         |

> 💡 **Tip:** For CI/CD (e.g. GitHub Actions), always use `--use-dot` for consistent layouts.

---

## Commands

### Build diagrams

Generate a production-ready static site from all `.c4` files under `src/c4`:

```bash
npm run build:c4
```

**Under the hood:**

```bash
likec4 build src/c4 -o ./dist --use-dot --use-hash-history
```

Output → `dist/`

---

### Preview diagrams

Build + serve locally for quick preview:

```bash
npm run preview:c4
```

**Equivalent to:**

```bash
npm run build:c4 && npx serve dist -l 62001
```

Then open → [http://127.0.0.1:62001](http://127.0.0.1:62001)

---

### Export to JSON

Export your LikeC4 model for further processing:

```bash
npm run export:c4
```

**Equivalent to:**

```bash
likec4 export json src/c4 --outfile ./exports/views.json --use-dot
```

---

## CI / CD ( GitHub Pages )

This repository includes a ready-to-use GitHub Actions workflow:
`.github/workflows/deploy.yml`

It:

1. Installs Graphviz
2. Builds diagrams with `npm run build:c4`
3. Publishes `dist/` to GitHub Pages

```yaml
- name: Install Graphviz
  run: sudo apt-get update && sudo apt-get install -y graphviz
```

> Your pages will be served automatically at:
> **https://<user>.github.io/<repo>/**

---

## Optional: Fallback Build Script

If Graphviz is unavailable (e.g. in lightweight containers):

```bash
if command -v dot &> /dev/null; then
  echo "✅ Graphviz found → using binary layout"
  npx likec4 build src/c4 --use-dot
else
  echo "⚠️ Graphviz missing → falling back to WASM layout"
  npx likec4 build src/c4
fi
```

---

## Example `likec4.config.json`

```json
{
  "$schema": "https://likec4.dev/schemas/config.json",
  "name": "project-name",
  "styles": {
    "defaults": {
      "border": "dashed",
      "opacity": 100,
      "size": "md",
      "color": "primary",
      "group": { "color": "primary", "opacity": 10, "border": "dashed" },
      "relationship": { "color": "gray", "line": "dashed", "arrow": "normal" }
    }
  }
}
```

---

## Example C4 File

```c4
system coordinator 'Coordinator Service' {
  component kafka 'Kafka Cluster'
  component mongo 'Mongo Cluster'
  component l2 'L2 Match Service'

  coordinator -> kafka 'produces match events'
  l2 -> mongo 'persists match data'
}
```