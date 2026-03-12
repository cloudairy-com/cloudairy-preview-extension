# Cloudairy VS Code Extension - Verification Documentation

## Overview

This document provides comprehensive verification procedures for the Cloudairy VS Code Extension. Use this guide to verify that all features, commands, and integrations work correctly before release or deployment.

**Extension Name:** Cloudairy  
**Version:** 1.0.0  
**Publisher:** cloudairy  
**Extension ID:** cloudairy.cloudairy

---

## Table of Contents

1. [Pre-Installation Verification](#pre-installation-verification)
2. [Installation Verification](#installation-verification)
3. [Extension Activation Verification](#extension-activation-verification)
4. [Command Verification](#command-verification)
5. [File Type Support Verification](#file-type-support-verification)
6. [Preview Functionality Verification](#preview-functionality-verification)
7. [Language Support Verification](#language-support-verification)
8. [Context Menu Verification](#context-menu-verification)
9. [Integration Verification](#integration-verification)
10. [Performance Verification](#performance-verification)
11. [Error Handling Verification](#error-handling-verification)
12. [UI/UX Verification](#uiux-verification)
13. [Dependencies Verification](#dependencies-verification)
14. [Cross-Platform Verification](#cross-platform-verification)
15. [Regression Testing](#regression-testing)

---

## Pre-Installation Verification

### Checklist

- [ ] **VS Code Version Compatibility**
  - Verify VS Code version is `^1.74.0` or higher
  - Check: `Help > About` or `code --version`
  - Expected: Version 1.74.0 or later

- [ ] **Package.json Validation**
  - Verify `package.json` exists and is valid JSON
  - Check required fields:
    - `name`: "Cloudairy"
    - `displayName`: "Cloudairy"
    - `version`: "1.0.0"
    - `publisher`: "cloudairy"
    - `main`: "./src/extension.js"
    - `engines.vscode`: "^1.74.0"
  - Verify all dependencies are listed correctly

- [ ] **File Structure Verification**
  - Verify `src/extension.js` exists
  - Verify `language-configuration.json` exists
  - Verify `resources/cloudairy-logo.png` exists
  - Verify `README.md` exists
  - Verify `LICENSE` exists

- [ ] **Package Validation**
  - Run: `npm install` (if applicable)
  - Verify no installation errors
  - Check for missing dependencies

---

## Installation Verification

### Manual Installation

1. **Package Extension**
   ```bash
   npm run package
   # or
   vsce package
   ```
   - [ ] Verify `.vsix` file is created successfully
   - [ ] Check file size is reasonable (not 0 bytes)
   - [ ] Verify file name format: `Cloudairy-1.0.0.vsix`

2. **Install from VSIX**
   - Open VS Code
   - Go to Extensions view (`Ctrl+Shift+X`)
   - Click `...` menu → `Install from VSIX...`
   - Select the `.vsix` file
   - [ ] Verify installation completes without errors
   - [ ] Verify extension appears in installed extensions list
   - [ ] Verify extension shows correct version (1.0.0)
   - [ ] Verify extension shows correct publisher (cloudairy)

3. **Verify Extension Details**
   - Click on the extension in Extensions view
   - [ ] Verify description matches: "Preview .cld (Cloudairy) files with interactive diagrams visualization"
   - [ ] Verify icon displays correctly
   - [ ] Verify README content displays properly
   - [ ] Verify categories: "Other", "Visualization"

### Marketplace Installation (if applicable)

- [ ] Search for "Cloudairy" in VS Code Extensions marketplace
- [ ] Verify extension appears in search results
- [ ] Verify extension can be installed from marketplace
- [ ] Verify installation completes successfully

---

## Extension Activation Verification

### Activation Events

The extension should activate on the following events:

- [ ] **onLanguage:cld**
  - Create a new file with `.cld` extension
  - Open the file
  - Verify extension activates (check Developer Tools console for activation messages)

- [ ] **onLanguage:json**
  - Create a new file with `.json` extension
  - Open the file
  - Verify extension activates

- [ ] **onCommand:cld.preview**
  - Execute command `cld.preview` from Command Palette
  - Verify extension activates

- [ ] **onCommand:cld.previewSideBySide**
  - Execute command `cld.previewSideBySide` from Command Palette
  - Verify extension activates

- [ ] **onStartupFinished**
  - Restart VS Code
  - Verify extension activates after startup

### Activation Verification Steps

1. Open Developer Tools: `Help > Toggle Developer Tools`
2. Go to Console tab
3. Perform activation triggers listed above
4. [ ] Verify no activation errors in console
5. [ ] Verify extension status shows as "Active" in Extensions view

---

## Command Verification

### Command: `cld.preview`

**Command ID:** `cld.preview`  
**Title:** "Cloudairy: Preview Diagram"  
**Category:** "CLD"  
**Icon:** `$(preview)`

#### Verification Steps

1. **Command Palette Access**
   - Press `Ctrl+Shift+P` (Windows/Linux) or `Cmd+Shift+P` (Mac)
   - Type "Cloudairy: Preview Diagram"
   - [ ] Verify command appears in palette
   - [ ] Verify command is executable

2. **Command Execution**
   - Open a `.cld` file
   - Execute `cld.preview` command
   - [ ] Verify preview panel opens
   - [ ] Verify diagram renders correctly
   - [ ] Verify no errors in console

3. **Command Availability**
   - [ ] Verify command available when `.cld` file is active
   - [ ] Verify command available when `cld` language is active
   - [ ] Verify command available when `json` language is active

### Command: `cld.previewSideBySide`

**Command ID:** `cld.previewSideBySide`  
**Title:** "Cloudairy: Preview Diagram"  
**Category:** "CLD"  
**Icon:** `$(split-horizontal)`

#### Verification Steps

1. **Command Palette Access**
   - Press `Ctrl+Shift+P` (Windows/Linux) or `Cmd+Shift+P` (Mac)
   - Type "Cloudairy: Preview Diagram"
   - [ ] Verify side-by-side preview command appears
   - [ ] Verify command is executable

2. **Command Execution**
   - Open a `.cld` file
   - Execute `cld.previewSideBySide` command
   - [ ] Verify side-by-side preview opens
   - [ ] Verify editor and preview are split horizontally
   - [ ] Verify diagram renders correctly
   - [ ] Verify no errors in console

3. **Command Availability**
   - [ ] Verify command available when `.cld` file is active
   - [ ] Verify command available when `cld` language is active
   - [ ] Verify command available when `json` language is active

---

## File Type Support Verification

### .cld File Extension

- [ ] **File Association**
  - Create a file with `.cld` extension
  - Verify VS Code recognizes it as a Cloudairy file
  - Verify language mode shows as "CLD" or "Cloudairy"

- [ ] **Syntax Highlighting** (if implemented)
  - Open a `.cld` file
  - Verify syntax highlighting works (if applicable)
  - Verify comments work (// and /* */)

- [ ] **Language Configuration**
  - Verify auto-closing pairs work: `{}`, `[]`, `""`, `''`
  - Verify surrounding pairs work
  - Verify bracket matching works

### Test Files

Create test `.cld` files with various content:

1. **Minimal Valid File**
   ```json
   {
     "type": "diagram",
     "nodes": []
   }
   ```
   - [ ] Verify preview works
   - [ ] Verify no errors

2. **Complex Diagram File**
   - Create a file with multiple nodes, edges, and configurations
   - [ ] Verify preview renders all elements
   - [ ] Verify diagram is interactive (if applicable)

3. **Invalid File**
   - Create a file with invalid JSON/syntax
   - [ ] Verify appropriate error handling
   - [ ] Verify error messages are user-friendly

---

## Preview Functionality Verification

### Preview Panel

- [ ] **Panel Creation**
  - Execute preview command
  - Verify preview panel/webview opens
  - Verify panel has correct title
  - Verify panel can be closed

- [ ] **Content Rendering**
  - Open a valid `.cld` file
  - Execute preview command
  - [ ] Verify diagram renders correctly
  - [ ] Verify all diagram elements are visible
  - [ ] Verify diagram is properly scaled
  - [ ] Verify diagram is interactive (if applicable)

- [ ] **Content Updates**
  - Open a `.cld` file
  - Execute preview command
  - Modify the `.cld` file content
  - [ ] Verify preview updates automatically (or verify update mechanism works)
  - [ ] Verify no errors during updates

- [ ] **Multiple Previews**
  - Open multiple `.cld` files
  - Open preview for each
  - [ ] Verify each preview works independently
  - [ ] Verify previews don't interfere with each other

### Side-by-Side Preview

- [ ] **Layout**
  - Execute side-by-side preview command
  - Verify editor and preview are split correctly
  - Verify split is horizontal
  - Verify both panels are visible

- [ ] **Synchronization**
  - Edit the `.cld` file in editor
  - [ ] Verify preview updates accordingly
  - [ ] Verify cursor position doesn't affect preview

- [ ] **Resizing**
  - Resize the split panels
  - [ ] Verify preview adjusts correctly
  - [ ] Verify diagram remains visible and properly scaled

---

## Language Support Verification

### CLD Language

- [ ] **Language Registration**
  - Verify `cld` language is registered in `package.json`
  - Verify language ID: `cld`
  - Verify aliases: `["CLD", "Cloudairy"]`
  - Verify extension: `.cld`
  - Verify configuration file: `./language-configuration.json`

- [ ] **Language Features**
  - Open a `.cld` file
  - [ ] Verify language mode is set to "CLD" or "Cloudairy"
  - [ ] Verify language configuration is applied
  - [ ] Verify comments work: `//` and `/* */`
  - [ ] Verify bracket matching works
  - [ ] Verify auto-closing pairs work

### JSON Language Support

- [ ] **JSON File Support**
  - Open a `.json` file
  - Verify extension commands are available
  - [ ] Verify preview can be opened for JSON files (if supported)

---

## Context Menu Verification

### Explorer Context Menu

- [ ] **Menu Item Visibility**
  - Right-click on a `.cld` file in Explorer
  - [ ] Verify "Cloudairy: Preview Diagram" appears in context menu
  - [ ] Verify menu item is in "navigation" group
  - [ ] Verify menu item only appears for `.cld` files

- [ ] **Menu Item Execution**
  - Right-click on a `.cld` file in Explorer
  - Click "Cloudairy: Preview Diagram"
  - [ ] Verify preview opens
  - [ ] Verify correct file is previewed

### Editor Context Menu

- [ ] **Menu Item Visibility**
  - Right-click in editor when `.cld` file is active
  - [ ] Verify "Cloudairy: Preview Diagram" appears in context menu
  - [ ] Verify menu item is in "navigation" group
  - [ ] Verify menu item only appears for `.cld` files

- [ ] **Menu Item Execution**
  - Right-click in editor when `.cld` file is active
  - Click "Cloudairy: Preview Diagram"
  - [ ] Verify preview opens
  - [ ] Verify correct file is previewed

---

## Integration Verification

### VS Code API Integration

- [ ] **Extension API Usage**
  - Verify extension uses VS Code API correctly
  - Check for proper API imports
  - [ ] Verify no deprecated APIs are used
  - [ ] Verify API calls are wrapped in try-catch blocks

### Webview API

- [ ] **Webview Creation**
  - Verify webview panels are created correctly
  - [ ] Verify webview content is properly loaded
  - [ ] Verify webview messages are handled correctly
  - [ ] Verify webview security is properly configured

### File System Integration

- [ ] **File Reading**
  - Verify `.cld` files are read correctly
  - [ ] Verify file encoding is handled properly
  - [ ] Verify large files are handled (if applicable)
  - [ ] Verify file watching works (for auto-updates)

---

## Performance Verification

### Load Time

- [ ] **Extension Activation**
  - Measure time from activation trigger to extension ready
  - [ ] Verify activation time is reasonable (< 2 seconds)
  - [ ] Verify no blocking operations during activation

- [ ] **Preview Rendering**
  - Measure time from command execution to preview visible
  - [ ] Verify rendering time is reasonable (< 3 seconds for typical files)
  - [ ] Verify large files don't cause significant delays

### Memory Usage

- [ ] **Memory Consumption**
  - Monitor memory usage with extension active
  - [ ] Verify memory usage is reasonable
  - [ ] Verify no memory leaks when opening/closing multiple previews
  - [ ] Verify memory is released when previews are closed

### CPU Usage

- [ ] **CPU Consumption**
  - Monitor CPU usage during preview rendering
  - [ ] Verify CPU usage is reasonable
  - [ ] Verify no excessive CPU usage during idle state

---

## Error Handling Verification

### Invalid File Handling

- [ ] **Invalid JSON**
  - Open a `.cld` file with invalid JSON
  - Execute preview command
  - [ ] Verify appropriate error message is displayed
  - [ ] Verify error message is user-friendly
  - [ ] Verify extension doesn't crash

- [ ] **Missing File**
  - Try to preview a file that doesn't exist
  - [ ] Verify appropriate error handling
  - [ ] Verify error message is clear

- [ ] **Empty File**
  - Open an empty `.cld` file
  - Execute preview command
  - [ ] Verify appropriate handling (error or empty preview)
  - [ ] Verify no crashes

### Network Errors (if applicable)

- [ ] **Offline Mode**
  - Disconnect from network (if extension uses network)
  - [ ] Verify graceful degradation
  - [ ] Verify appropriate error messages

### Resource Errors

- [ ] **Missing Resources**
  - Verify extension handles missing resources gracefully
  - [ ] Verify fallback behavior works

---

## UI/UX Verification

### Visual Elements

- [ ] **Icons**
  - Verify extension icon displays correctly
  - Verify command icons display correctly
  - Verify icons are appropriate size and resolution

- [ ] **Preview Appearance**
  - Verify preview has proper styling
  - Verify preview is readable
  - Verify preview matches VS Code theme (if applicable)
  - Verify preview is responsive

### User Experience

- [ ] **Command Discoverability**
  - Verify commands are easy to find in Command Palette
  - Verify context menu items are intuitive
  - Verify keyboard shortcuts work (if implemented)

- [ ] **Feedback**
  - Verify loading indicators appear (if applicable)
  - Verify error messages are clear
  - Verify success indicators (if applicable)

- [ ] **Accessibility**
  - Verify keyboard navigation works
  - Verify screen reader compatibility (if applicable)
  - Verify proper ARIA labels (if applicable)

---

## Dependencies Verification

### Required Dependencies

Verify all dependencies are correctly installed and functional:

- [ ] **@dagrejs/dagre** (^2.0.4)
  - Verify library loads correctly
  - Verify graph layout functionality works

- [ ] **d3-flextree** (^2.1.2)
  - Verify library loads correctly
  - Verify tree layout functionality works

- [ ] **d3-hierarchy** (^3.1.2)
  - Verify library loads correctly
  - Verify hierarchy functionality works

- [ ] **dompurify** (^3.3.2)
  - Verify library loads correctly
  - Verify HTML sanitization works (if used)

- [ ] **jsdom** (^28.1.0)
  - Verify library loads correctly
  - Verify DOM functionality works (if used)

- [ ] **lodash** (^4.17.23)
  - Verify library loads correctly
  - Verify utility functions work

- [ ] **mermaid** (^10.9.5)
  - Verify library loads correctly
  - Verify diagram rendering works

### Dependency Conflicts

- [ ] **Version Conflicts**
  - Verify no version conflicts with VS Code or other extensions
  - Verify all dependencies are compatible

- [ ] **Bundle Size**
  - Verify extension bundle size is reasonable
  - Verify dependencies don't bloat the extension

---

## Cross-Platform Verification

### Windows

- [ ] **Installation**
  - Verify extension installs correctly on Windows
  - Verify all features work on Windows

- [ ] **File Paths**
  - Verify file paths are handled correctly (Windows uses `\`)
  - Verify no path-related errors

### macOS

- [ ] **Installation**
  - Verify extension installs correctly on macOS
  - Verify all features work on macOS

- [ ] **File Paths**
  - Verify file paths are handled correctly (macOS uses `/`)
  - Verify no path-related errors

### Linux

- [ ] **Installation**
  - Verify extension installs correctly on Linux
  - Verify all features work on Linux

- [ ] **File Paths**
  - Verify file paths are handled correctly (Linux uses `/`)
  - Verify no path-related errors

---

## Regression Testing

### Previous Version Compatibility

- [ ] **Backward Compatibility**
  - Verify extension works with older VS Code versions (if supported)
  - Verify existing `.cld` files still work

### Feature Stability

- [ ] **Core Features**
  - Verify preview functionality hasn't regressed
  - Verify commands still work
  - Verify context menus still work

### Known Issues

- [ ] **Issue Tracking**
  - Review known issues from previous versions
  - Verify fixes are still in place
  - Verify no new regressions introduced

---

## Test Cases Summary

### Critical Test Cases (Must Pass)

1. ✅ Extension installs without errors
2. ✅ Extension activates correctly
3. ✅ Preview command opens preview panel
4. ✅ Side-by-side preview works
5. ✅ `.cld` files are recognized
6. ✅ Diagrams render correctly
7. ✅ No crashes on invalid files
8. ✅ Context menus work

### Important Test Cases (Should Pass)

1. ✅ Preview updates when file changes
2. ✅ Multiple previews work independently
3. ✅ Large files are handled
4. ✅ Error messages are user-friendly
5. ✅ Performance is acceptable

### Nice-to-Have Test Cases

1. ✅ Preview matches VS Code theme
2. ✅ Keyboard shortcuts work
3. ✅ Accessibility features work

---

## Verification Checklist Template

Use this checklist for each verification session:

```
Date: ___________
Tester: ___________
VS Code Version: ___________
OS: ___________

Pre-Installation: [ ] Pass [ ] Fail
Installation: [ ] Pass [ ] Fail
Activation: [ ] Pass [ ] Fail
Commands: [ ] Pass [ ] Fail
File Types: [ ] Pass [ ] Fail
Preview: [ ] Pass [ ] Fail
Language: [ ] Pass [ ] Fail
Context Menus: [ ] Pass [ ] Fail
Integration: [ ] Pass [ ] Fail
Performance: [ ] Pass [ ] Fail
Error Handling: [ ] Pass [ ] Fail
UI/UX: [ ] Pass [ ] Fail
Dependencies: [ ] Pass [ ] Fail
Cross-Platform: [ ] Pass [ ] Fail
Regression: [ ] Pass [ ] Fail

Overall Status: [ ] Pass [ ] Fail
Notes: 
_______________________________________
_______________________________________
_______________________________________
```

---

## Reporting Issues

If verification fails, report issues with:

1. **Issue Description**
   - What feature/functionality failed
   - Steps to reproduce
   - Expected behavior
   - Actual behavior

2. **Environment Details**
   - VS Code version
   - Operating system
   - Extension version
   - Other relevant details

3. **Error Information**
   - Error messages
   - Console logs
   - Screenshots (if applicable)

4. **Severity**
   - Critical: Blocks core functionality
   - High: Major feature broken
   - Medium: Minor feature broken
   - Low: Cosmetic issue

---

## Conclusion

This verification documentation should be used before:
- Publishing to VS Code Marketplace
- Releasing new versions
- Major code changes
- Before user acceptance testing

Regular verification ensures the extension maintains quality and reliability.

**Last Updated:** [Date]  
**Version:** 1.0.0  
**Maintained By:** Cloudairy Team
