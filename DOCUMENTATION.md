# Cloudairy File Preview Extension - Complete Documentation

## Table of Contents
1. [Project Overview](#project-overview)
2. [Architecture](#architecture)
3. [Installation & Setup](#installation--setup)
4. [How It Works](#how-it-works)
5. [File Structure](#file-structure)
6. [Key Components](#key-components)
7. [Extension Features](#extension-features)
8. [Development Guide](#development-guide)
9. [CLD File Format](#cld-file-format)
10. [Troubleshooting](#troubleshooting)

---

## Project Overview

**Cloudairy File Preview Extension** is a Visual Studio Code extension that provides interactive visualization and preview capabilities for `.cld` (Cloudairy) diagram files. The extension allows developers to:

- Preview `.cld` files with interactive diagrams
- Visualize flowcharts, mindmaps, ER diagrams, and sequence diagrams
- Export diagrams as PNG, JPEG, or SVG
- Support for Cloudairy syntax parsing
- Interactive pan, zoom, and node manipulation
- Theme switching (black/white backgrounds)
- Edge type customization (step edges, curved edges)
- Real-time file watching and auto-refresh

### Technology Stack
- **Runtime**: Node.js (VS Code Extension API)
- **Parsing**: Custom logic for diagram parsing
- **Rendering**: Custom SVG-based rendering engine
- **Layout**: Custom layout engine using dagre.js algorithms
- **UI**: VS Code Webview API for interactive previews

---

## Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    VS Code Extension                         │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────┐      ┌──────────────────┐                │
│  │ extension.js │──────▶│ PreviewProvider  │                │
│  │ (Entry Point)│      │  (Main Handler)  │                │
│  └──────────────┘      └────────┬─────────┘                │
│                                   │                           │
│                                   ▼                           │
│                          ┌──────────────────┐                │
│                          │   parser.js      │                │
│                          │  (CLD Parser)    │                │
│                          └────────┬─────────┘                │
│                                   │                           │
│                                   ▼                           │
│                    ┌──────────────────────────┐              │
│                    │  CloudairyCodeParser.js    │              │
│                    │  (Diagram Type Handler)  │              │
│                    └─────────────┬────────────┘              │
│                                  │                            │
│              ┌───────────────────┼───────────────────┐       │
│              │                   │                   │       │
│              ▼                   ▼                   ▼       │
│      ┌─────────────┐   ┌─────────────┐   ┌─────────────┐  │
│      │ Flowchart   │   │  Mindmap     │   │ ER Diagram  │  │
│      │ Parser      │   │  Parser      │   │ Parser      │  │
│      └─────────────┘   └─────────────┘   └─────────────┘  │
│                                                               │
│                                   ▼                           │
│                          ┌──────────────────┐                │
│                          │  LayoutEngine    │                │
│                          │  (Node Layout)   │                │
│                          └────────┬─────────┘                │
│                                   │                           │
│                                   ▼                           │
│                    ┌──────────────────────────┐              │
│                    │   Webview HTML Content    │              │
│                    │   (Interactive Preview)   │              │
│                    └──────────────────────────┘              │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### Data Flow

1. **User Action**: User opens a `.cld` file or triggers preview command
2. **File Reading**: Extension reads the `.cld` file content
3. **Parsing**: Content is parsed by `CloudairyCodeParser` to extract nodes and edges
4. **Layout Calculation**: `LayoutEngine` calculates optimal node positions
5. **HTML Generation**: `PreviewProvider` generates interactive HTML with SVG
6. **Webview Display**: VS Code displays the preview in a webview panel
7. **User Interaction**: User can pan, zoom, drag nodes, change themes, export

---

## Installation & Setup

### Prerequisites

- **Node.js**: Version 14.x or higher
- **npm**: Version 6.x or higher (comes with Node.js)
- **VS Code**: Version 1.74.0 or higher
- **VS Code Extension Development Tools**: `@vscode/vsce` package

### Installation Steps

#### 1. Clone or Download the Project

```bash
# Navigate to your workspace
cd /path/to/workspace

# If using git
git clone <repository-url>
cd cloudairy-file-preview-extension
```

#### 2. Install Dependencies

```bash
npm install
```

This will install all required dependencies including:
- `@dagrejs/dagre` - Graph layout algorithms
- `d3-flextree` - Tree layout algorithms
- `d3-hierarchy` - Hierarchy data structures
- `lodash` - Utility functions
- `jsdom` - DOM implementation for Node.js
- `dompurify` - HTML sanitization
- `@vscode/vsce` - VS Code extension packaging tool

#### 3. Build the Extension

The extension is written in JavaScript and doesn't require compilation. However, you can package it:

```bash
npm run package
```

This creates a `.vsix` file that can be installed in VS Code.

#### 4. Install in VS Code

**Option A: Install from VSIX file**
1. Open VS Code
2. Go to Extensions view (Ctrl+Shift+X)
3. Click the "..." menu at the top
4. Select "Install from VSIX..."
5. Choose the generated `.vsix` file

**Option B: Development Mode**
1. Open the project folder in VS Code
2. Press `F5` or go to Run > Start Debugging
3. A new VS Code window opens with the extension loaded

#### 5. Verify Installation

1. Open a `.cld` file in VS Code
2. Right-click on the file
3. Select "Cloudairy: Preview Diagram"
4. The preview should open in a side panel

---

## How It Works

### Extension Activation

The extension activates when:
- A `.cld` file is opened
- The preview command is triggered
- VS Code starts (if configured)

**Entry Point**: `src/extension.js`

```javascript
function activate(context) {
    // Register preview provider
    const provider = new PreviewProvider(context.extensionUri);
    
    // Register commands
    vscode.commands.registerCommand('cld.preview', (uri) => {
        provider.showPreview(uri, true);
    });
}
```

### File Parsing Process

#### Step 1: File Content Reading
```javascript
// previewProvider.js - updatePreview()
const fileContent = fs.readFileSync(resource.fsPath, 'utf8');
```

#### Step 2: CLD File Parsing
```javascript
// parser.js
const cldData = await parseCLDFileContent(fileContent);
```

#### Step 3: Cloudairy Parsing
```javascript
// CloudairyCodeParser.js
const parser = await CloudairyDiagramParser(code);
await parser.parse(); // Validates and parses Cloudairy syntax
const graph = await parser.getGraph(); // Extracts nodes and edges
```

#### Step 4: Layout Calculation
```javascript
// LayoutEngine.js
const layoutEngine = new LayoutEngine({ direction: 'LR' });
const updatedNodes = layoutEngine.layout(nodes, edges);
```

#### Step 5: HTML Generation
```javascript
// previewProvider.js
const html = this.getWebviewContent(cldData);
this._panel.webview.html = html;
```

### Supported Diagram Types

#### 1. Flowcharts
- **Syntax**: Cloudairy flowchart syntax
- **Example**:
  ```cloudairy
  flowchart LR
    A[Start] --> B{Decision}
    B -->|Yes| C[Process]
    B -->|No| D[End]
  ```
- **Node Types**: Process (square), Decision (diamond), Connector (stadium)
- **Features**: Automatic layout, edge labels, color coding

#### 2. Mindmaps
- **Syntax**: Cloudairy mindmap syntax
- **Example**:
  ```cloudairy
  mindmap
    root((Root))
      Child1
      Child2
        Grandchild
  ```
- **Features**: Hierarchical tree layout, automatic positioning

#### 3. ER Diagrams
- **Syntax**: Custom ER diagram format
- **Example**:
  ```
  Entities
  1. User
  - id (int, PK)
  - name (string)
  
  Relationships
  1. User (1) — (M) Address
  - Address.user_id → User.id
  ```
- **Features**: Table-based entity visualization, relationship mapping

### Interactive Features

#### Pan and Zoom
- **Pan**: Click and drag on empty canvas
- **Zoom**: Mouse wheel or zoom buttons (+/-)
- **Fit to Screen**: Automatically adjusts view to show all nodes

#### Node Manipulation
- **Drag Nodes**: Click and drag nodes to reposition
- **Real-time Edge Updates**: Edges automatically update when nodes move

#### Theme Switching
- **Black Theme**: Dark background with white edges
- **White Theme**: Light background with black edges

#### Edge Customization
- **Step Edges**: Orthogonal edges with 90-degree turns
- **Curved Edges**: Smooth bezier curves
- **Solid/Dashed**: Toggle between solid and dashed line styles

#### Export Functionality
- **PNG Export**: Raster image export
- **JPEG Export**: Compressed image export
- **SVG Export**: Vector graphics export

---

## File Structure

```
cloudairy-file-preview-extension/
│
├── package.json                 # Extension manifest and dependencies
├── package-lock.json            # Dependency lock file
├── README.md                    # User-facing documentation
├── DOCUMENTATION.md             # This file - technical documentation
├── language-configuration.json  # CLD language configuration
├── LICENSE                      # License file
│
├── resources/
│   └── cloudairy-logo.png       # Extension logo
│
└── src/
    ├── extension.js             # Extension entry point
    ├── previewProvider.js       # Main preview handler (3200+ lines)
    ├── parser.js                # CLD file parser wrapper
    ├── CloudairyCodeParser.js     # Cloudairy syntax parser (1290 lines)
    ├── LayoutEngine.js          # Graph layout algorithms
    ├── Node.js                  # Node data model
    ├── Edge.js                  # Edge data model
    ├── utils.js                 # Utility functions
    ├── const.js                 # Constants and configurations
    │
    ├── cld-files/               # Example CLD files
    │   ├── demo.cld
    │   ├── flowchart.cld
    │   ├── mindmap.cld
    │   └── mindmap-old.cld
    │
    └── demo.cld                 # Example CLD file
```

### Key Files Explained

#### `extension.js`
- **Purpose**: Extension entry point and command registration
- **Key Functions**:
  - `activate()`: Initializes extension
  - Command handlers for preview commands
  - Webview panel serializer for persistence

#### `previewProvider.js`
- **Purpose**: Core preview functionality
- **Key Responsibilities**:
  - Webview panel management
  - HTML content generation
  - File watching and auto-refresh
  - Download handling
  - Error handling and display
- **Size**: ~3200 lines (largest file)

#### `CloudairyCodeParser.js`
- **Purpose**: Parse Cloudairy syntax and extract diagram data
- **Key Methods**:
  - `validate()`: Validates Cloudairy syntax
  - `parse()`: Parses diagram into internal representation
  - `_parseFlowchart()`: Extracts flowchart nodes/edges
  - `_parseMindmap()`: Extracts mindmap structure
  - `_parseERDiagram()`: Extracts ER diagram entities/relationships

#### `LayoutEngine.js`
- **Purpose**: Calculate optimal node positions
- **Algorithm**: Uses dagre.js for automatic layout
- **Features**: Direction support (LR, TB, RL, BT), spacing control

#### `parser.js`
- **Purpose**: Wrapper around CloudairyCodeParser
- **Function**: `parseCLDFileContent()` - Main parsing entry point

---

## Key Components

### PreviewProvider Class

**Location**: `src/previewProvider.js`

**Main Methods**:

1. **`showPreview(resource, sideBySide)`**
   - Opens or updates preview panel
   - Handles panel lifecycle
   - Sets up file watchers

2. **`updatePreview(resource)`**
   - Reads file content
   - Parses CLD data
   - Generates HTML
   - Updates webview

3. **`getWebviewContent(cldData)`**
   - Generates complete HTML with embedded JavaScript
   - Includes SVG rendering code
   - Handles user interactions

4. **`getLoadingSkeletonHtml(webview)`**
   - Shows loading state while parsing
   - Displays extension logo

5. **`getErrorHtml(errorMessage)`**
   - Displays error messages
   - User-friendly error handling

### CloudairyCodeParser Class

**Location**: `src/CloudairyCodeParser.js`

**Main Methods**:

1. **`validate()`**
   - Validates Cloudairy syntax
   - Handles ER diagram format
   - Returns boolean

2. **`parse()`**
   - Parses diagram using Custom logic
   - Initializes internal database
   - Detects diagram type

3. **`extract()`**
   - One-call API for full extraction
   - Returns nodes, edges, type, title

4. **`_parseFlowchart()`**
   - Extracts flowchart nodes and edges
   - Applies default styling
   - Calculates node positions

5. **`_parseMindmap()`**
   - Builds hierarchical tree structure
   - Creates parent-child relationships
   - Applies tree layout

6. **`_parseERDiagram()`**
   - Parses custom ER diagram format
   - Creates table-based entity nodes
   - Maps relationships

### LayoutEngine Class

**Location**: `src/LayoutEngine.js`

**Purpose**: Automatic graph layout using dagre.js

**Features**:
- Direction support (LR, TB, RL, BT)
- Configurable spacing
- Automatic node positioning
- Edge routing

### Node and Edge Classes

**Location**: `src/Node.js`, `src/Edge.js`

**Purpose**: Data models for diagram elements

**Node Properties**:
- `id`: Unique identifier
- `position`: X, Y coordinates
- `width`, `height`: Dimensions
- `data`: Styling and content
- `type`: Node type (Process, Decision, etc.)

**Edge Properties**:
- `id`: Unique identifier
- `source`, `target`: Node IDs
- `label`: Edge label text
- `style`: Stroke color, width
- `type`: Edge type (step-edge, editable-edge)

---

## Extension Features

### 1. File Preview

**Command**: `cld.preview`

**Usage**:
- Right-click on `.cld` file → "Cloudairy: Preview Diagram"
- Command Palette (Ctrl+Shift+P) → "Cloudairy: Preview Diagram"
- Automatically opens side-by-side with editor

### 2. Auto-Refresh

- **File Watcher**: Monitors `.cld` file for changes
- **Auto-Update**: Preview refreshes automatically on save
- **Real-time**: Changes appear immediately

### 3. Interactive Controls

**Toolbar Features**:
- **Zoom In/Out**: Adjust zoom level
- **Fit to Screen**: Auto-fit all nodes
- **Theme Toggle**: Switch between black/white themes
- **Edge Type**: Toggle between step and curved edges
- **Edge Stroke**: Toggle solid/dashed lines
- **Download**: Export as PNG, JPEG, or SVG

### 4. Node Interaction

- **Drag**: Click and drag nodes to reposition
- **Hover**: Visual feedback on hover
- **Selection**: Click to select nodes

### 5. Pan and Zoom

- **Pan**: Click and drag on canvas
- **Zoom**: Mouse wheel or buttons
- **Zoom Display**: Shows current zoom percentage

### 6. Export Options

**Supported Formats**:
- **PNG**: High-quality raster image
- **JPEG**: Compressed image format
- **SVG**: Scalable vector graphics

**Export Process**:
1. User clicks download button
2. Selects format from dropdown
3. SVG is serialized or converted to canvas
4. File save dialog appears
5. File is saved to selected location

---

## Development Guide

### Setting Up Development Environment

1. **Install VS Code Extension Development Tools**:
   ```bash
   npm install -g @vscode/vsce
   ```

2. **Open Project in VS Code**:
   ```bash
   code .
   ```

3. **Install Dependencies**:
   ```bash
   npm install
   ```

### Running in Development Mode

1. **Press F5** or go to Run > Start Debugging
2. **New VS Code Window Opens**: Extension is loaded in Extension Development Host
3. **Test Changes**: Make changes, reload window (Ctrl+R)

### Debugging

1. **Set Breakpoints**: Click in gutter next to line numbers
2. **Debug Console**: View logs and errors
3. **Developer Tools**: Right-click webview → "Inspect" to debug HTML/JS

### Making Changes

#### Adding New Diagram Type

1. **Update CloudairyCodeParser.js**:
   ```javascript
   case "newType":
       return { type: type, title: title, ...this._parseNewType() };
   ```

2. **Implement Parser Method**:
   ```javascript
   _parseNewType() {
       // Extract nodes and edges
       return { nodes: [], edges: [] };
   }
   ```

3. **Update Preview HTML**: Modify `getWebviewContent()` if needed

#### Adding New Features

1. **Add Command** (in `extension.js`):
   ```javascript
   const newCommand = vscode.commands.registerCommand('cld.newFeature', () => {
       // Implementation
   });
   context.subscriptions.push(newCommand);
   ```

2. **Update package.json**:
   ```json
   "contributes": {
       "commands": [{
           "command": "cld.newFeature",
           "title": "New Feature"
       }]
   }
   ```

### Testing

1. **Manual Testing**:
   - Open various `.cld` files
   - Test all interactive features
   - Verify error handling

2. **Test Files**: Use files in `src/cld-files/` directory

### Packaging

```bash
npm run package
```

Creates `.vsix` file in project root.

### Publishing

1. **Install vsce**:
   ```bash
   npm install -g @vscode/vsce
   ```

2. **Package Extension**:
   ```bash
   vsce package
   ```

3. **Publish to Marketplace**:
   ```bash
   vsce publish
   ```

---

## CLD File Format

### What is a CLD File?

CLD (Cloudairy) files are text files containing diagram definitions. They support:

1. **Cloudairy Syntax**: Standard Cloudairy diagram syntax
2. **ER Diagram Format**: Custom entity-relationship format
3. **JSON Format**: Direct node/edge JSON (legacy)

### Cloudairy Syntax Support

#### Flowchart
```cloudairy
flowchart LR
  A[Start] --> B{Decision}
  B -->|Yes| C[Process]
  B -->|No| D[End]
```

#### Mindmap
```cloudairy
mindmap
  root((Root))
    Child1
    Child2
      Grandchild
```

### ER Diagram Format

```
Entities
1. User
- id (int, PK)
- name (string, NOT NULL)
- email (string, UNIQUE)

2. Order
- id (int, PK)
- user_id (int, FK)
- total (decimal)

Relationships
1. User (1) — (M) Order
- Order.user_id → User.id
```

### JSON Format (Legacy)

```json
{
  "boardName": "My Diagram",
  "nodes": [
    {
      "id": "node1",
      "position": { "x": 100, "y": 100 },
      "data": { "label": "Node 1" }
    }
  ],
  "edges": [
    {
      "id": "edge1",
      "source": "node1",
      "target": "node2"
    }
  ]
}
```

---

## Troubleshooting

### Common Issues

#### 1. Extension Not Activating

**Symptoms**: Preview command not available

**Solutions**:
- Check VS Code version (requires 1.74.0+)
- Reload window (Ctrl+Shift+P → "Reload Window")
- Check extension is enabled in Extensions view

#### 2. Preview Not Showing

**Symptoms**: Blank preview or error message

**Solutions**:
- Check file has `.cld` extension
- Verify file contains valid Cloudairy syntax
- Check Developer Console for errors (Help > Toggle Developer Tools)

#### 3. Parsing Errors

**Symptoms**: "Invalid CLD file" error

**Solutions**:
- Check for syntax errors in diagram code
- Ensure proper diagram type declaration (flowchart, mindmap, etc.)

#### 4. Layout Issues

**Symptoms**: Nodes overlapping or off-screen

**Solutions**:
- Use "Fit to Screen" button
- Check node positions in file
- Verify LayoutEngine is working correctly

#### 5. Export Not Working

**Symptoms**: Download fails or creates empty file

**Solutions**:
- Check file permissions
- Verify sufficient disk space
- Try different export format

### Debugging Tips

1. **Enable Logging**:
   - Open Developer Console (Help > Toggle Developer Tools)
   - Check Console tab for errors

2. **Check Extension Logs**:
   - View > Output
   - Select "CLD Preview" from dropdown

3. **Inspect Webview**:
   - Right-click preview panel
   - Select "Inspect" to open DevTools

4. **Test with Sample Files**:
   - Use files in `src/cld-files/` directory
   - Verify they work correctly

### Getting Help

1. **Check Documentation**: Review this file and README.md
2. **VS Code Issues**: Check VS Code version compatibility
4. **Extension Logs**: Review output panel for detailed errors

---

## Technical Details

### Dependencies

**Production Dependencies**:
- `@dagrejs/dagre@^2.0.4` - Graph layout
- `d3-flextree@^2.1.2` - Tree layout
- `d3-hierarchy@^3.1.2` - Hierarchy structures
- `lodash@^4.17.23` - Utilities
- `jsdom@^28.1.0` - DOM implementation
- `dompurify@^3.3.2` - HTML sanitization

**Development Dependencies**:
- `@vscode/vsce@^2.22.0` - Extension packaging

### VS Code API Usage

**Key APIs Used**:
- `vscode.window.createWebviewPanel()` - Preview panel
- `vscode.workspace.createFileSystemWatcher()` - File watching
- `vscode.commands.registerCommand()` - Command registration
- `vscode.window.showSaveDialog()` - File save dialog

### Performance Considerations

1. **Large Diagrams**: May slow down with 100+ nodes
2. **File Watching**: Only watches active preview file
3. **Memory Usage**: SVG rendering is memory-efficient
4. **Parsing**: Cloudairy parsing is async to prevent blocking

### Security

1. **Content Security Policy**: Webview uses strict CSP
2. **HTML Sanitization**: User content is sanitized
3. **File Access**: Only reads files user has access to
4. **No Network Access**: Extension doesn't make network requests

---

## Conclusion

This documentation provides a comprehensive overview of the Cloudairy File Preview Extension. For additional information:

- **User Guide**: See README.md
- **VS Code Extension API**: https://code.visualstudio.com/api
- **Extension Marketplace**: https://marketplace.visualstudio.com/

---

**Last Updated**: 2024
**Version**: 1.0.0
**Maintainer**: Cloudairy Team
