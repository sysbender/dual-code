# dual-code

 

# 🧾 **1. Project Requirements Document**

## 1.1 **Project Name**

**TS/Python Dual Runtime Code Playground**

## 1.2 **Goal / Purpose**

Create a **web-based interactive playground** where users can:

* View **TypeScript code** and **Python code** side-by-side
* **Edit** the code in the browser
* **Run** the code directly in the browser
* See the **execution output** in a unified console
* Use it for **language comparisons**, teaching, documentation, or demos
* Host it on **any static site** (GitHub Pages, Netlify, Vercel)

No backend server should be required.

---

## 1.3 **Features**

### **Core Features**

| Feature               | Description                                                         |
| --------------------- | ------------------------------------------------------------------- |
| TS Editor             | Interactive code editor with syntax highlighting and error checking |
| Python Editor         | Interactive code editor backed by Pyodide                           |
| Run TS                | Button compiles TS → JS → executes in browser                       |
| Run Python            | Button executes Python via Pyodide                                  |
| Shared Output Console | Displays stdout, errors, logs                                       |
| Reset Code            | Restore original examples                                           |
| Side-by-Side Layout   | TS and Python editors in split view                                 |
| Zero Backend          | Entire app runs client-side                                         |

---

### **Extended/Nice-to-Have Features**

| Feature              | Description                              |
| -------------------- | ---------------------------------------- |
| Dark/Light theme     | Toggle for UI                            |
| URL Save/Share       | Encode code in URL or use localStorage   |
| Code Comparison Mode | Highlight differences in logic/structure |
| Multiple Examples    | Dropdown to switch sample programs       |
| Download Code        | Export code snippets                     |
| Embed Mode           | Allow iframe embedding in other pages    |

---

## 1.4 **Non-functional Requirements**

| Category      | Requirement                                      |
| ------------- | ------------------------------------------------ |
| Performance   | Editors load < 2 seconds on modern browsers      |
| Compatibility | Works on Chrome, Edge, Firefox, Safari           |
| Deployment    | Must run entirely in static hosting environment  |
| Security      | No eval on raw user input without sandboxing     |
| Size          | Pyodide loads lazily to reduce load time         |
| Accessibility | Keyboard navigation supported; readable contrast |

---

## 1.5 **Technical Constraints**

* Must use **Pyodide** for Python execution (WASM CPython)
* Must use **TypeScript compiler (ts.transpile)** or **SWC** for TS execution
* Must use a **browser-based code editor**:

  * Monaco Editor (recommended)
  * or CodeMirror 6
* Must not require any backend or database
* Must work offline once loaded

---

# 🧱 **2. System Design**

## 2.1 **High-Level Architecture**

```
+-------------------------------------------------------------+
|                         Web Page                            |
|                                                             |
|  +----------------+    +----------------+     +-----------+ |
|  | TS Editor      |    | Python Editor  |     | Output    | |
|  | (Monaco)       |    | (Monaco)       |---->| Console   | |
|  +----------------+    +----------------+     +-----------+ |
|         |                        |                      |   |
|         v                        v                      |   |
|   TS Compiler (ts.js)     Pyodide Runtime (WASM) <------+   |
|         |                        |                          |
|         v                        v                          |
|    Execute JS              Execute Python                    |
+-------------------------------------------------------------+
```

Both languages run in the browser; no server required.

---

## 2.2 **Key Components**

### **1. TypeScript Runtime Module**

* Uses **TypeScript compiler**:
  `typescriptServices.js` in browser
* Steps to run TS:

  1. Get editor code content
  2. Transpile TS → JS
  3. Execute JS inside safe iframe or sandboxed eval
* Errors shown in console

---

### **2. Python Runtime Module**

* Uses **Pyodide** WASM runtime
* Steps:

  1. Load `pyodide.js`
  2. Initialize Pyodide runtime (lazy load)
  3. Run Python code: `pyodide.runPython(code)`
  4. Capture stdout/stderr and feed into shared console

---

### **3. Monaco Editor Integration**

* Two monaco instances:

  * `editorTS`
  * `editorPY`
* Common config:

  * Auto-complete
  * Syntax highlighting
  * Error markers
  * Theming

---

### **4. Shared Console**

Handles:

* Normal output
* Error output
* Logs from both runtimes
* Clear function on each run

---

## 2.3 **UI Layout**

### Desktop Layout:

```
┌─────────────────────┬──────────────────────┐
│   TypeScript Editor │    Python Editor     │
│                     │                      │
└─────────────────────┴──────────────────────┘
┌────────────────────────────────────────────┐
│                  Console                   │
└────────────────────────────────────────────┘
```

### Mobile Layout:

Tabs:

```
[ TypeScript | Python | Output ]
```

---

## 2.4 **Data Flow**

### Running TS:

```
TS Editor → ts.transpile → JS Code → Execution → Console Output
```

### Running Python:

```
Python Editor → Pyodide runtime → Execution → Console Output
```

---

## 2.5 **Error Handling**

* TS compile errors shown in console
* Python exceptions caught and printed nicely
* Pyodide loading failures handled with retry messaging
* JS execution wrapped in try/catch

---

## 2.6 **Dependencies**

### Mandatory

* **Pyodide** (Python runtime)
* **TypeScript Compiler**
* **Monaco Editor**

### Optional

* Prettier or Black (formatting)
* Tailwind or custom CSS

---

## 2.7 **Security Considerations**

* All execution sandboxed in browser environment
* No network access allowed from Python unless explicitly configured
* Using iframe-sandbox for JS execution recommended

---

# 🧩 **3. Future Enhancements**

* Add WebAssembly-backed Python libs (e.g., numpy, micropip installs)
* Add JS/TS ↔ Python interoperability
* Split-pane resizable UI
* Run multiple files per language
* Export & save playground sessions

 
Tell me:
**“Generate the full playground HTML file”** and I’ll output the complete code.
