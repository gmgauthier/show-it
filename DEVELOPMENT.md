# Show-It development plan

A gtkmm-3 presentation editor for LCOS. The window is PowerPoint 97. The file is a simple `.pptx`.

Display name: **Show-It**  
Binary / repo / package: `show-it`  
APP_ID: `org.gmgauthier.ShowIt`  
License: The Unlicense (`UNLICENSE`)  
Repos: https://gitea.scriptorium/gmgauthier/show-it (origin), https://github.com/gmgauthier/show-it

## Status (2026-10-07)

**Specification.** This repository holds the plan. Source begins at M0, after Write-It and Count-It v1 have been lived with. v1 is M0 through M5. Live with that set before M6. Tag `v0.1.0` at M5.

The 960×700 first-launch mockup is [brand/window.png](brand/window.png). The sample deck in that picture is `market.pptx`.

Show-It absorbs the 2026-09-12 Presentable sketch. Do not open a `presentable` repository.

## 1. Locked decisions

| Decision | Choice |
|---|---|
| Product | Original. The window is PowerPoint 97. Title, bullets, one picture, outline, sorter, F5 |
| Name | Show-It. Binary `show-it` |
| Toolkit | C++17, gtkmm-3.0, GTK3 CSS, Meson (`warning_level=2`). Cairo or a `Gtk::DrawingArea` for the canvas |
| Look | One decorated window. Clearlooks-Phenix draws the controls. The window manager draws the title bar |
| File | A simple `.pptx` (PresentationML, ECMA-376), written with libarchive and libxml2 |
| Fallback | An outline of titles and bullets, plain text or Markdown. Save still writes `.pptx` |
| Show | F5. Click or space advances. Esc returns to edit |
| Init | No systemd. Config `~/.config/show-it/show-it.ini` |
| License | The Unlicense |
| Versioning | Semantic (`MAJOR.MINOR.PATCH`). When source exists, `meson.build` is the source of truth. The Debian changelog and git tag `vX.Y.Z` match it |

## 2. Place in the suite

Retro-Office is three applications with one window language: Write-It (`write-it`), Count-It (`count-it`), and Show-It. Each is its own repository. The umbrella note lives in the local `lcos-projects` folder as `RETRO-OFFICE.md` and is not part of this repository. This file is the Show-It specification.

AbiWord started as the first piece of AbiSuite. GNOME Office was AbiWord plus Gnumeric. The deck never landed. Show-It is that missing piece. There is still no small GTK presentation editor with a real feature set. Calligra Stage is the smaller conventional editor, and it is Qt. LibreOffice Impress is the completeness ceiling. Spice-up is a simple GTK canvas. Pinpoint, Impressive, and pdfpc only present slides.

This product is three things glued together: a drawing editor, a document model, and a fullscreen player. Unbounded, it becomes Impress. The work is to keep it small.

Write-It is the first codebase, then Count-It, then this one. Pictures as paintings stay with Lunduke Paint. Show-It is a deck. Organized notes stay in the Ephemeris Notepad.

## 3. House rules

- Devuan Excalibur / LCOS, XLibre, XFCE, Clearlooks-Phenix
- No systemd, no PackageKit, no custom title bar, no daemon, no online account, no AI
- Local files only
- Borrow the LCOS palette. Do not use Bryan’s seal
- Ship `.deb`, source tarball, and AppImage at M5
- Tests headless and offline. Lint covers `src/` only

## 4. Window

One deck, one window. The title is `Show-It - market.pptx`. A dirty deck adds a trailing `*`. A new deck is `Show-It - Untitled`.

Closing a dirty deck asks one question. The buttons, in order, are **Save**, **Don’t Save**, **Cancel**. Save is the default. **Close** (Ctrl+W) returns to Untitled. **Exit** (Ctrl+Q) leaves the program. The first launch is 960×700, not maximized. The window remembers its size.

The slide sits on a neutral gray pasteboard, `#808080`, the same gray Write-It uses. The left list starts at 200 pixels.

```
+------------------------------------------------------------------+
| File  Edit  View  Insert  Format  Tools  Slide Show  Help        |
+------------------------------------------------------------------+
| [New] [Open] [Save] | [Print] | [Cut] [Copy] [Paste] | [Undo] [Redo]
| [Font ▾] [Size ▾] [B] [I] [U] | [Left] [Center] [Right] | [Bullets]
+------------------------+-----------------------------------------+
| Outline / Sorter       |  Slide canvas, on the gray pasteboard   |
|  Saturday market       |                                         |
|    Tomatoes            |  title + bullets + picture              |
|    Bread               |                                         |
|  The list              |                                         |
|  Closing               |                                         |
+------------------------+-----------------------------------------+
| Drawing: Text  Image                                             |
+------------------------------------------------------------------+
| Slide view                               Slide 1 of 3      100%  |
+------------------------------------------------------------------+
```

Icons come from the desktop icon theme, by freedesktop name: `document-new`, `document-open`, `document-save`, `document-print`, `edit-cut`, `edit-copy`, `edit-paste`, `edit-undo`, `edit-redo`, `format-text-bold`, `format-text-italic`, `format-text-underline`, `format-justify-left`, `format-justify-center`, `format-justify-right`. Toolbars are icons. The menu’s words are the tooltip. A missing icon falls back to that short word. A toolbar combo that applies a format returns focus to the canvas afterward.

Cut, Copy, Paste, Undo, and Redo are insensitive when there is nothing to do. Save stays sensitive. The right-click menu starts with Cut, Copy, Paste, then a separator, then this app’s own items.

### Menus

The menus are File, Edit, View, Insert, Format, Tools, Slide Show, Help. Mnemonics: **F**ile, **E**dit, **V**iew, **I**nsert, F**o**rmat, **T**ools, **S**lide Show, **H**elp. A menu item that opens a dialog ends with `…`. Accelerators are visible in the menu.

**File.** Open’s filter lists `.pptx` and the outline. Save writes `.pptx`. Export writes the outline.

| Item | Keys | What it does |
|---|---|---|
| New | Ctrl+N | A blank untitled deck |
| New from Template… | | Pick a starter deck of the native type |
| Open… | Ctrl+O | Remember the last directory |
| Open Recent | | Up to eight basename items. The tooltip is the full path. A missing file uses one sentence: “That file is missing.” |
| Save | Ctrl+S | Write the whole `.pptx` |
| Save As… | | |
| Export… | | Outline of titles and bullets |
| Print… | Ctrl+P | The system print dialog. Handouts wait |
| Page Setup… | | Paper, orientation, margins |
| Close | Ctrl+W | Back to Untitled |
| Exit | Ctrl+Q | |

**Edit.**

| Item | Keys |
|---|---|
| Undo | Ctrl+Z |
| Redo | Ctrl+Y, and Ctrl+Shift+Z |
| Cut | Ctrl+X |
| Copy | Ctrl+C |
| Paste | Ctrl+V |
| Delete | Delete. Removes the selected box |
| Select All | Ctrl+A |
| Find… | Ctrl+F |
| Replace… | Ctrl+H |

Find and Replace are one modal dialog. Fields, in order: Find, Replace, a Match case check, Next, Replace, Close.

**View.**

| Item | Behaviour |
|---|---|
| Standard Toolbar | Check. On by default |
| Format Toolbar | Check. On by default |
| Status Bar | Check. On by default |
| Zoom | Submenu: 50%, 75%, 100%, 150%, 200%, Fit width |
| Outline / Sorter / Slide | Radio. Slide is the default |

**Insert.** Picture…, New Slide, Text Box.

**Format.** Font…, Bold (Ctrl+B), Italic (Ctrl+I), Underline (Ctrl+U), Align Left, Center, Align Right, then Bullets on the body box. Font… is family, size, bold, italic, underline. No colour in v1.

The font list and the size list match the other two apps. Sizes are 8, 9, 10, 11, 12, 14, 16, 18, 24, 36. A new document starts at Sans 11. The master may draw the title larger. The toolbar still comes up at 11 until the selection says otherwise.

**Tools.** Options…: default font family, default size, recent-file count (4, 8, or 12). Spelling is Write-It’s item. The Tools menu stays so the menu bar does not shift.

**Slide Show.** Start Show (F5).

**Help.** About Show-It. The dialog shows the program name, the version, one sentence, the Unlicense, and Close.

### Keyboard

The only keyboard is the PowerPoint 97 map in the menus above. F5 starts the show of this deck. F10 is the GTK menu key. Alt+F4 closes the window. F1 is Help. F7 is unused here.

### Toolbars

The standard toolbar never grows an app-specific button. Groups, left to right: New Open Save, Print, Cut Copy Paste, Undo Redo.

The format toolbar: font, size, bold, italic, underline, align left, align center, align right, then a separator, then bullets.

The drawing bar is a full-width band directly above the status bar. v1 is Text and Image. Line and other autoshapes wait. The bar has a place to grow, and v1 does not fill it.

### Status bar

The left side is a message (“Slide view”) that stays until the next message. The rightmost cell is the zoom, and it pops the same list as View. The cell to its left is the slide, “Slide 3 of 12”.

### Views

PowerPoint 97 is a small set of views around one deck:

- **Slide** — one canvas: text boxes and pictures. This is the default
- **Outline** — titles and bullets as a tree
- **Sorter** — thumbnail strip, drag to reorder
- **Notes** — later
- **Slide Show** — F5, click or space, Esc. A fullscreen window, not a browser. No presenter-view dual head in v1

The 90s transitions were wipes and flies, not a compositor. v1 has no transition editor. Advance is click, space, and Esc.

### Toolkit

gtkmm-3 window, `Gtk::Paned`, and a left list (outline or sorter). Cairo, or a `Gtk::DrawingArea`, for the canvas. Prefer the poorer renderer if it keeps the 97 feeling.

### Config

`~/.config/show-it/show-it.ini`

Keys: `window-width`, `window-height`, `recent`, `last-dir`, `default-font`, `default-size`, `show-standard-toolbar`, `show-format-toolbar`, `show-statusbar`, `zoom`.

## 5. Feature floor

- New / Open / Save a deck
- Title, bullets, and one picture per slide
- A simple master that stamps title and body
- Outline, sorter, edit, and an F5 show
- Print handouts later

No WordArt, no Graph charts, no animation timeline, no SmartArt, and no complete PowerPoint file. The v1 drawing bar is text and image.

## 6. Format

The file Show-It saves is **`.pptx`** (PresentationML, ECMA-376). It is a zip of XML, written with libarchive and libxml2. The skeleton for a minimum deck is fixed: a presentation part, one slide master, one slide layout, a theme, and one slide file per slide. The picture sits in a media folder beside the slides. Title, bullets, and that one picture are the slide. Slide order is the sorter. PowerPoint and LibreOffice Impress open this slice.

The 90s file was binary OLE `.ppt`. Show-It does not clone that format. `.odp` waits. A private tag set is unnecessary once the deck is this slice of `.pptx`.

The v1 contract is the `.pptx` Show-It writes, plus a straightforward title-and-bullet deck that uses that same slice. Animation, SmartArt, and embedded video stay out. PDF waits with the handouts.

**An outline is the fallback, and it is lossy.** Export writes titles and bullets as plain text or Markdown. Import builds slides from that outline. The picture and the master stay in the `.pptx`. Outline export is a separate command.

## 7. Work plan

| Milestone | Done when |
|---|---|
| **M0 — Window** | Menus, toolbars, paned outline stub, empty canvas, About. Matches the sketch. |
| **M1 — Deck file** | New / Open / Save the `.pptx`. Title, bullets, and one picture per slide persist. |
| **M2 — Edit** | Click to select a box; type; insert a picture; a simple master stamps title and body. Outline edits titles. |
| **M3 — Sorter + show** | Thumbnail strip, drag to reorder. F5 fullscreen; click or space advances; Esc returns to edit. |
| **M4 — Polish** | Keys, last-file restore, `show-it.ini`, status `Slide n of m`. Outline import and export. |
| **M5 — Package** | `debian/`, `scripts/release.sh` → `.deb`, tarball, AppImage. Tag `v0.1.0`. |

Print handouts and PDF, ODP, extra autoshapes, and the rest of PresentationML: after v1.

## 8. Traps

- Parsing Office binary `.ppt`
- Taking the rest of PresentationML, or “just support ODP,” without a locked subset
- Animation and transition editors
- Charts, tables-as-spreadsheet, video on slides
- Themes that are a second product
- Anything that needs a daemon or a cloud
- Presenter view / dual head in v1
- WordArt, SmartArt, or a drawing bar that grows past Text and Image in v1
- Opening a separate `presentable` repository
