# C4 Viewer

A minimal **C4 architecture diagram viewer** powered by [LikeC4](https://likec4.dev).
This setup uses the **LikeC4 CLI only** (no React, no Vite) for generating, exporting, and previewing interactive architecture diagrams.

---

## Features

- 🚀 Fast CLI-based workflow — no frontend build required
- 🧩 Uses **Graphviz** for layout rendering (via system binary or WebAssembly)
- ⚙️ Perfect for local and CI/CD use
- 📄 Supports HTML builds, JSON exports, and static previews

---

## Installation

Install dependencies:

```bash
npm install
```

Then install **Graphviz**, which is required for the high-performance `--use-dot` mode.

### Install Graphviz

**macOS:**

```bash
brew install graphviz
```

**Ubuntu / Debian:**

```bash
sudo apt update && sudo apt install graphviz -y
```

**Fedora / RHEL:**

```bash
sudo dnf install graphviz -y
```

**Alpine (for CI containers):**

```bash
apk add --no-cache graphviz
```

Check installation:

```bash
dot -V
```

---

## 🧠 WASM vs Binary Layout

LikeC4 uses Graphviz internally to calculate how diagrams are laid out.
You can choose **two layout backends** depending on your environment:

| Mode                       | Description                                                   | Pros                                                                              | Cons                                                                                          |
| -------------------------- | ------------------------------------------------------------- | --------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| **`wasm` (default)**       | Uses Graphviz compiled to **WebAssembly**, built into LikeC4. | ✅ No installation needed<br>✅ Works anywhere Node runs                          | ❌ Slower for large diagrams<br>❌ Higher memory usage<br>❌ Can freeze on very complex views |
| **`binary` (`--use-dot`)** | Uses your **local Graphviz binary (`dot`)** for layout.       | ✅ Fastest and most stable<br>✅ Handles large diagrams easily<br>✅ Lower memory | ❌ Requires Graphviz installed                                                                |

### Summary

> **WASM** = Portable and self-contained
> **Binary** = Native and high-performance

If Graphviz is installed, **always use binary layout** for production or CI builds.

---

## 🧩 Commands

### 1. Build diagrams

Generates a production-ready static build of all `.c4` files under `src/c4`:

```bash
npm run build:c4
```

**Under the hood:**

```bash
likec4 build src/c4 --use-dot
```

Output will be written to `src/c4/dist/`.

---

### 2. Preview diagrams

Starts a local web server for previewing generated diagrams:

```bash
npm run preview:c4
```

**Under the hood:**

```bash
npm run build:c4 && likec4 preview src/c4/dist
```

Default preview URL:
👉 [http://127.0.0.1:62001/](http://127.0.0.1:62001/)

---

### 3. Export to JSON

Export your LikeC4 model for integration with pipelines, dashboards, or analysis tools:

```bash
npx likec4 export json -o ./exports/likec4.json src/c4
```

---

## Optional: Safe Fallback for Pipelines

If Graphviz isn’t available (e.g. in lightweight CI containers), you can fall back to WASM automatically:

```bash
if command -v dot &> /dev/null; then
  echo "Graphviz found → using binary layout"
  npx likec4 build src/c4 --use-dot
else
  echo "Graphviz not found → using wasm layout"
  npx likec4 build src/c4
fi
```

---

## Example `.likec4rc.json`

```json
{
  "workspace": "src/c4",
  "outDir": "dist",
  "layout": "binary",
  "printErrors": false
}
```

---

## Example `.c4` File

```c4
system coordinator 'Coordinator Service' {
  component kafka 'Kafka Cluster'
  component mongo 'Mongo Cluster'
  component l2 'L2 Match Service'
  coordinator -> kafka 'produces match events'
  l2 -> mongo 'persists match data'
}
```
