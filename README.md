# PSConsole

A Windows-native PowerShell IDE and script runner built as a .NET 4.8 WPF application. No runtime installation required on client machines.
![PSConsole App](https://github.com/user-attachments/assets/309e8f4d-840b-4398-9822-d63aa821e695)
---

# End-User Guide

PSConsole is a PowerShell editor and script runner built for IT administrators. It handles the tasks that come up repeatedly in day-to-day scripting work — writing, running, packaging, and deploying scripts — without requiring you to switch between multiple tools.

---

## Elevate to Administrator

The toolbar shows an **Administrator** button whenever PSConsole is running without administrator rights. Clicking it relaunches the application with full admin privileges via UAC, and carries your current workspace over automatically: every open folder, file, and unsaved change transfers to the elevated session. Once elevated, the button disappears and the title bar reflects the administrator context.

![PSConsole TopButtons](https://github.com/user-attachments/assets/8c13de6b-89f2-425a-90da-4a7130bf055d)
---

## Compile a Script Folder into a Standalone EXE

Right-clicking any root folder in the Explorer that contains at least one `.ps1` file reveals a **Create EXE** option.

The EXE that gets built is entirely self-contained. Every file in the root folder — additional `.ps1` modules, DLLs, MSI/EXE installers, configuration files, anything — is packaged inside the EXE as binary resources. When the EXE runs, it extracts the full folder structure to a temp directory, then executes the entry script from there. `$PSScriptRoot` resolves correctly, so dot-sourced imports, `Add-Type` paths, and `Start-Process` calls all work exactly as they do when running the script directly.

Two build modes are available:

- **Standard User** — the EXE runs with the privileges of whoever launches it and extracts to `%TEMP%`
- **Require Administrator** — the EXE checks for admin rights on startup and triggers a UAC prompt if needed, then extracts to `C:\Windows\Temp`

Execution policy is bypassed at the PowerShell API level, not by calling `Set-ExecutionPolicy`. This means the EXE runs on machines with `Restricted` or `AllSigned` policy — including machines where Group Policy has locked policy changes — without any user action required.

Named parameters defined in the entry script's `param()` block work as expected. `.\Deploy.exe -Uninstall`, `.\Deploy.exe -Mode Silent`, and similar invocations behave the same as calling the original `.ps1` directly. `Write-Host` colors are preserved when running from a console window.

![PSConsole CreateEXE](https://github.com/user-attachments/assets/bdabb048-be79-48db-96be-4d0663c40833)
---

## Package a Folder as an Intunewin

Right-clicking any root folder reveals a **Create Intunewin** option. PSConsole locates candidate setup files (`.exe`, `.msi`, `.ps1`) in the folder, prompts you to choose the setup file if more than one is found, then downloads `IntuneWinAppUtil.exe` from Microsoft's official GitHub repository if it is not already present at `C:\Windows\Temp\IntuneWinAppUtil\`. The resulting `.intunewin` package is written to the same root folder. Compilation progress streams to the console pane in real time.

---

## Auto-Formatting with PSScriptAnalyzer

When the PSScriptAnalyzer module is installed, PSConsole formats PowerShell code automatically using the same `Invoke-Formatter` engine that the VS Code PowerShell extension uses. Formatting happens in three ways:

- **On demand** via the Format toolbar button
- **In real time** as you type — closing a `}` reformats the enclosing block; pressing Enter reformats the line just completed
- **On every save** — the full document is formatted before writing to disk

If PSScriptAnalyzer is not installed, an Install button appears in the toolbar. The install runs in the background via the PowerShell Gallery and requires no administrator rights.

---

## New Project Templates

**File → New Project** creates a folder with a pre-built `Install.ps1` that includes a `param` block with an `-Uninstall` switch, a `Write-Log` function, and automatic dot-sourcing of any additional `.ps1` files placed in sub-folders. An optional **Software preset** adds a `Software_Functions` folder containing two fully-featured functions: `Install-Software` (handles EXE, MSI, and MSP installers with silent flags and exit code interpretation) and `Remove-Software` (locates and removes software by display name pattern from the registry, with version filtering, multiple fallback uninstall methods, and a structured result object).

![PSConsole NewProject](https://github.com/user-attachments/assets/32d49230-48a5-4389-a361-2c21a6e72ef5)
---

## Set as Default Editor for PowerShell Files

**File → Associate File Types** registers PSConsole as the default application for `.ps1`, `.psm1`, `.psd1`, `.log`, `.md`, and `.txt` files. Double-clicking any of these opens directly in PSConsole. Registration writes to `HKCU` only and does not require administrator rights. A corresponding **Remove File Associations** option undoes the registration.

When PSConsole is already open and you double-click a `.ps1` file, the file opens in the existing window rather than launching a second instance. If the file's folder is already in the Explorer tree, PSConsole navigates to it; otherwise the folder is added automatically.

![PSConsole SetFileDefault](https://github.com/user-attachments/assets/1a0bc06b-c06e-42ca-a308-016da494e93b)
---

## Multi-Tab Editor

Multiple files stay open in tabs simultaneously. An amber dot on a tab marks unsaved changes. Each tab maintains its own independent undo history — navigating away from a file and back preserves the full Ctrl+Z stack. Right-clicking any tab offers **Close Other Tabs** and **Close All Tabs**.

---

## Auto Session

The **Auto Session** toggle controls what happens when PSConsole closes. With it on, every open folder, tab, and unsaved edit is captured silently on close and restored exactly on the next launch — including dirty files that were never saved to disk. With it off, PSConsole prompts about unsaved changes and offers **Remember Session** as one of the options in that dialog.

---

## Built-in Terminal

The console pane at the bottom runs a live PowerShell session. Commands entered there persist in the same session as the script editor — functions defined by a script you run with F5 are immediately available at the prompt. Command history navigates with Up/Down arrow. Tab completion works in the command input. Output is color-coded by stream: errors, warnings, verbose, debug, information, and host output each render in a distinct color matching the active theme.

---

## Two Themes

The **Theme** toolbar button cycles between two built-in themes. **Neon** uses a deep purple background with high-contrast cyan, magenta, and yellow token colors. **Dark** uses a near-black background with a more subdued palette closer to VS Code's dark defaults. Both themes apply to the editor, the syntax highlighting, and the console pane simultaneously.

![PSConsole DarkTheme](https://github.com/user-attachments/assets/537817cc-6b83-4d5c-bc7a-4704ddb11716)
---

## Multiple Explorer Windows

Right-clicking a root folder in the Explorer tree and choosing **Open in New Window** moves that root to a brand-new PSConsole window. The root is transferred, not duplicated — it disappears from the current window and appears in the new one. Each window is an independent process. Closing one does not affect any other open windows.

---

---