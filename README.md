# C Development & Debugging in GitHub Codespaces (VS Code)

A streamlined setup for compiling and debugging single-file or multi-file C programs inside GitHub Codespaces (or Docker-based VS Code Remote Containers) using **GCC** and **CodeLLDB**.

---

## 🛠 Why CodeLLDB?

In containerized environments such as GitHub Codespaces, default `gdb` (`cppdbg`) sessions often hang or deadlock with the error:
```text
warning: GDB: Failed to set controlling terminal: Operation not permitted
```
**CodeLLDB** circumvents container pseudo-terminal (`pty`) permission restrictions, ensuring smooth breakpoint hits, variable inspection, and standard I/O redirection.

---

## 📦 Prerequisites

1. **GitHub Codespaces** connected to local VS Code (or browser VS Code).
2. **GCC** installed inside the container:
   ```bash
   sudo apt-get update && sudo apt-get install -y build-essential
   ```
3. **Extensions** (installed *within* Codespace):
   - [C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) (for IntelliSense and syntax highlighting)
   - [CodeLLDB](https://marketplace.visualstudio.com/items?itemName=vadimcn.vscode-lldb) (for debugging)

---

## ⚙️ Configuration Files

Create or edit the configuration files under the `.vscode/` directory at the root of your workspace.

### 1. Build Task: `.vscode/tasks.json`

This task automatically builds the currently active `.c` file using `gcc` with debug symbols (`-g`) and optimizations disabled (`-O0`).

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "type": "shell",
      "label": "C/C++: gcc build active file",
      "command": "/usr/bin/gcc",
      "args": [
        "-g",
        "-O0",
        "${file}",
        "-o",
        "${fileDirname}/${fileBasenameNoExtension}"
      ],
      "options": {
        "cwd": "${fileDirname}"
      },
      "problemMatcher": [
        "$gcc"
      ],
      "group": {
        "kind": "build",
        "isDefault": true
      },
      "detail": "Compiles the active C file with debug symbols."
    }
  ]
}
```

---

### 2. Debug Configuration: `.vscode/launch.json`

This configuration hooks into **CodeLLDB**, invokes the build task before launching (`preLaunchTask`), and runs the compiled binary for the active file.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "LLDB: Build and debug active file",
      "type": "lldb",
      "request": "launch",
      "program": "${fileDirname}/${fileBasenameNoExtension}",
      "args": [],
      "cwd": "${fileDirname}",
      "preLaunchTask": "C/C++: gcc build active file",
      "terminal": "integrated"
    }
  ]
}
```

---

## 🚀 How to Run & Debug

1. **Open your C source file** (e.g., `main.c` or `hello.c`) in the editor.
2. **Set breakpoints** by clicking in the gutter next to the line numbers (a red dot will appear).
3. **Start Debugging**:
   - Press **`F5`** (or go to `Run` > `Start Debugging`).
4. **Step Through Code**:
   - Use the debug toolbar to Step Over (`F10`), Step Into (`F11`), or Continue (`F5`).
   - Inspect variables and call stacks in the **Run & Debug** side panel.
   - View console output in the **Terminal** / **Debug Console** tab.

---

## 🔍 VS Code Variable Reference

| Variable | Description |
| :--- | :--- |
| `${file}` | Full path to the currently active open file in the editor |
| `${fileDirname}` | Directory of the currently active file |
| `${fileBasenameNoExtension}` | Filename without extension (used for binary name) |
| `${workspaceFolder}` | Root folder path opened in VS Code / Codespaces |