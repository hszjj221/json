# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **single-page JSON Viewer web application** contained entirely within `index.html`. It is a zero-dependency, vanilla JavaScript application with no build process.

## Running the Application

```bash
# Open directly in browser
open index.html

# Or serve with a simple HTTP server
python -m http.server 8000
```

## Architecture

### Single-File Structure

The entire application exists in one file (~64KB) with three sections:
1. **CSS (lines 8-829)**: Theming, responsive layout, diff visualization styles
2. **HTML (lines 831-949)**: DOM structure with dual panes (input + tree view)
3. **JavaScript (lines 951-2060)**: All application logic

### Core JavaScript Modules

**State Management:**
- `lastParsed`, `lastParsed2` - parsed JSON objects for main/diff panels
- `selected` - currently selected tree node
- `isDiffMode` - comparison mode flag
- `searchMatches`, `diffMatches` - search/diff results

**Key Functions:**
- `parseAndRender()` - main entry point, handles JSON parsing with error reporting
- `renderTreeAsync()` - chunked DOM rendering via `requestIdleCallback` (400 nodes/chunk)
- `makeNode()` - recursive tree node creation with collapsible children
- `deepDiff()` - diff algorithm using LCS for arrays, recursive comparison for objects
- `applyFilter()` - search with regex/predicate support and highlighting
- `navigateSearch()`, `navigateDiff()` - keyboard navigation through results

**Utilities:**
- `valueType()` - determine JSON value type
- `isDeepEqual()` - deep equality comparison
- `joinPath()` - JSON Pointer path construction
- `formatPath()` - convert between JSON Pointer, dot notation, bracket notation

### DOM Structure

```
.shell
├── .title (header with theme toggle)
├── .mobile-tabs (mobile navigation only)
├── .panes
│   ├── .pane#pane-left (input section)
│   │   ├── .editor-unit#unit-1 (main editor)
│   │   └── .editor-unit#unit-2 (diff editor, hidden by default)
│   ├── .divider (resizable split pane)
│   └── .pane#pane-right (tree view)
│       ├── .search-container
│       ├── .diff-nav (diff navigation, hidden by default)
│       └── .tree (rendered JSON tree)
├── #toast (notifications)
└── #context-menu (right-click menu)
```

### Theming System

Uses CSS Custom Properties with `data-theme` attribute:
- Default: Dark theme (`--bg: #1e1e1e`)
- Light theme: `[data-theme="light"]` (`--bg: #ffffff`)
- Theme preference stored in `localStorage: json-viewer-theme`

### Diff Algorithm

The diff system (`deepDiff()`) handles:
- **Objects**: Recursive property comparison
- **Arrays**: LCS (Longest Common Subsequence) dynamic programming algorithm
- **Merging**: Adjacent removed/added pairs become "changed" nodes

### Performance Optimizations

- Chunked async rendering via `requestIdleCallback` (400 nodes per chunk)
- Debounced input (300ms) for search and auto-parse
- Synchronized scrolling between dual editors

### Local Storage Keys

- `json-viewer-theme` - 'light' or 'dark'
- `json-viewer-split` - split pane ratio (0.2-0.8)
- `json-viewer-autoparse` - auto-parse enabled ('1' or '0')

## Development Notes

- No build process, package.json, or external dependencies
- Manual testing only (no test framework)
- Mobile-first responsive design with 768px breakpoint
- Context menu supports both right-click (desktop) and long-press (mobile)
