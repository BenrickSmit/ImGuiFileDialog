# ImGuiFileDialog

This document outlines the necessary steps to integrate and use the `ImGuiFileDialog` (ImGui File Dialog) library within a project, including a patch for compatibility with the current version of `ImGui`.

## Compatibility Patch

The version of `ImGuiFD` fetched from the `main` branch is not compatible with the current versions of `ImGui` used. This leads to a compilation error due to a change in the `ImGui::RenderTextEllipsis` function signature.

To fix this, I needed to modify `ImGuiFD.cpp`.

## Update Patch - Icon Replacement

Although I really enjoyed the ASCII art for the files, I wanted to use more modern Icons. I set out to use the Material Symbols font from Google.

-   **Modified `ImGuiFD.cpp`** to include this header and use `ICON_MD_FOLDER` and `ICON_MD_ARTICLE` for folder and file icons respectively.
-   The `asciiArtIcons` setting has been removed.
-   **Icon Fallback**: If the Material Design icon font is not loaded, the file dialog will automatically revert to using text-based indicators (e.g., `[DIR]`, `[FILE]`) for folders and files.

## Usage

To use `ImGuiFD`, you need to follow a specific pattern of calling `OpenDialog` and `BeginDialog`.

### 1. Opening the Dialog

To open a file dialog, call `ImGuiFD::OpenDialog`. This is typically done in response to a button click or menu item selection.

```cpp
if (ImGui::Button("Save")) {
    // Opens a directory selection dialog
    ImGuiFD::OpenDialog(
        "SaveDialog",               // A unique ID for the dialog
        ImGuiFDMode_OpenDir,        // The mode (e.g., open directory)
        "."                         // The initial path
    );
}
```

### 2. Rendering the Dialog

The dialog itself must be rendered within your main UI loop (e.g., your `OnUIRender` function). The `ImGuiFD::BeginDialog` function handles the rendering and logic of the dialog.

```cpp
// In your main UI loop
if (ImGuiFD::BeginDialog("SaveDialog")) {
    if (ImGuiFD::ActionDone()) {
        if (ImGuiFD::SelectionMade()) {
            // User made a selection
            std::string selectedPath = ImGuiFD::GetSelectionPathString(0);
            // Do something with the path...
        }
        ImGuiFD::CloseCurrentDialog();
    }
    ImGuiFD::EndDialog();
}
```

**Important:**
- The ID used in `BeginDialog` must match the ID used in `OpenDialog`.
- Do not call `BeginDialog` inside the same block as `OpenDialog`. `OpenDialog` signals that a dialog should be opened, and `BeginDialog` renders it in the main loop.
- Use unique IDs for different dialogs to avoid conflicts.
