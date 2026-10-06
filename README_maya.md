# UV Shell Gap Overlay for Maya

A Maya tool that shows, on your UV layout, how many texture pixels separate neighboring UV shells, how far shells sit from their UDIM tile border, and the texel density of every shell. Gap measurements are color-graded against the padding you need. Texel density is color-graded against the density you need, in the tool's UV view and on the mesh in the 3D viewport. Each shell gets one compact info block with its density, its object's scale, whether it is flipped, and an arrow showing which way is up in the scene. Material sets let you select and hide the shells of chosen materials and measure gaps only within a material. Problems are called out where they happen: overlapping shells, shells crossing a tile border, and flipped (mirrored) shells.

Shells that lie on each other on purpose, such as copies of a part sharing one piece of the texture, count as one shell and can be moved to another UDIM tile with one click. The UV view also has the basic UV tools: select UVs, edges, faces or shells, cut, weld, move, rotate and scale.

It is the Maya version of the UV Shell Gap Overlay add-on for Blender and measures the same way.

**Version:** 1.1.0 · **Maya:** 2022 to 2026 (Windows, macOS, Linux) · **License:** GPL-3.0-or-later

**New in 1.1**
- Much faster on scenes with many objects: the work is done in the background, object by object, and only what changed is done again. Hiding a material set or switching the arrows no longer measures anything again.
- Stacks: copies lying exactly on each other are measured as one shell and are no longer reported as overlaps. Only shells that don't fit are overlaps.
- **Move All but One** takes every shell lying on another to the next UDIM tile and leaves one unflipped shell of each stack in place.
- UV tools in the window: pick UVs, edges, faces and shells in the view; cut, weld, weld by distance; move, rotate and scale by dragging or by numbers.
- The UV view is drawn on the graphics card.

---

## Features

- **The UV Gaps window.** A dockable window with your UV layout and the full overlay. It follows the meshes you select and updates as you edit UVs, move objects or assign materials. Maya doesn't let Python tools draw inside its own UV Editor, so the overlay has a view of its own.
- **Shell-to-shell gaps in pixels.** Measurement points run along every shell border, holes included. Each point draws a line to the nearest neighboring shell and labels it with the distance in texture pixels.
- **Color gradient.** Red at 0 px, yellow at your *Minimal* gap, green at your *Needed* gap and above.
- **Overlaps and stacks.** Shells lying on each other on purpose are a *stack*: they are measured once and shown with one info block (`× 6`). Shells that overlap without fitting, such as a copy placed carelessly or two shells packed into each other, are labeled **Overlap** where they cross. See [Overlaps and stacks](#overlaps-and-stacks).
- **Move the copies away.** One button moves all shells lying on others to another UDIM tile, keeping one shell of every stack, an unflipped one, where it is.
- **UV tools.** Select UVs, edges, faces or whole shells in the view by clicking or dragging a rectangle. Cut, weld, weld within a distance. Move, rotate and scale with a manipulator, or by exact amounts with buttons. See [UV tools](#uv-tools).
- **Same material only.** Gaps, overlaps and stacks can be limited to shells of the same material, since each material usually has its own texture.
- **UDIM tile borders.** Shells near the edge of their tile show their distance to it, with separate *Border Minimal / Needed* thresholds. Shells crossing a tile line are flagged.
- **Flipped shells.** Mirrored shells are highlighted, and a single button selects them.
- **Texel density per shell.** Every shell is tinted by its texel density: red at your *Low* value, green at *Needed*, blue at *High*, with a gradient in between. Values are shown in px/cm, px/m, px/in or px/ft. See [Texel density](#texel-density).
- **Auto Low / High.** *Low* and *High* can fill themselves with the lowest and highest density of the material sets you check, and stay editable.
- **Select by texel density.** A two-handle range over the analyzed densities selects every shell inside it. The shells in range are outlined.
- **Texel density in the 3D viewport.** The same colors on the meshes themselves (Viewport 2.0).
- **One info block per shell.** Texel density, object scale (when it is not 1), *Flipped*, and an optional orientation arrow share one box per shell, so they never cover each other.
- **Orientation arrows.** An arrow on each shell points where the scene's up axis runs across it, in Maya's axis colors.
- **Material sets.** A list of the materials on the shown meshes, with shell counts and density ranges. Select, hide or reveal the shells of one material or of several checked ones.
- **Adjustable.** Texture size (presets, custom non-square, or the image shown in the UV Editor), number of points, point position along the border, font size, opacity, line width, and colors. Settings are saved with the scene, and you can save your own defaults.
- **Fast.** Shells are found and gaps measured in the background, so Maya stays responsive and the layout appears before the gaps do. Results are kept per object and per material. See [Performance](#performance).

---

## Installation

The tool needs NumPy, which Maya doesn't include. The installer takes care of it.

1. Unzip the download anywhere. Keep `install.py` and the `UVShellGapOverlay` folder side by side.
2. Drag `install.py` into a Maya viewport.
3. If Maya's Python has no NumPy yet, you are asked whether to install it. Maya's own pip then downloads it (internet needed) into Maya's folder for your scripts, so no administrator rights are needed.
4. A **UV Gaps** button is added to the current shelf, and the window opens.

The installer copies the `UVShellGapOverlay` folder into the `modules` folder of your Maya user folder, so every Maya version you run finds it. NumPy, however, belongs to each Maya version's Python: when you start another Maya version for the first time, the window offers to install NumPy there too.

| Your Maya user folder | |
|---|---|
| Windows | `Documents\maya` |
| macOS | `~/Library/Preferences/Autodesk/maya` |
| Linux | `~/maya` |

**Updating from 1.0.** Drag the new version's `install.py` into a viewport. It closes the window, replaces the module folder and opens the window again. Your settings, saved defaults and shelf button stay.

**Installing NumPy yourself.** If the automatic install fails (no internet, a firewall), run Maya's `mayapy` from a terminal, adjusting the year to your Maya version, then restart Maya:

```
Windows:  "C:\Program Files\Autodesk\Maya2024\bin\mayapy.exe" -m pip install --target "%USERPROFILE%\Documents\maya\2024\scripts\site-packages" "numpy<2"
macOS:    /Applications/Autodesk/maya2024/Maya.app/Contents/bin/mayapy -m pip install --target ~/Library/Preferences/Autodesk/maya/2024/scripts/site-packages "numpy<2"
Linux:    /usr/autodesk/maya2024/bin/mayapy -m pip install --target ~/maya/2024/scripts/site-packages "numpy<2"
```

NumPy 1.x is used because it has builds for the Python of every Maya from 2022 on. If Maya's Python already has NumPy, that one is used.

**Installing by hand.** Copy the `UVShellGapOverlay` folder into `<your Maya user folder>/modules`, and next to it create a text file `UVShellGapOverlay.mod` containing one line, with the full path of the copied folder:

```
+ UVShellGapOverlay 1.1.0 C:/Users/you/Documents/maya/modules/UVShellGapOverlay
```

Restart Maya and run `import uvgap; uvgap.show()` in a Python tab of the Script Editor (that line also makes a good shelf button).

**Uninstalling.** Delete `UVShellGapOverlay` and `UVShellGapOverlay.mod` from the `modules` folder, and remove the shelf button. NumPy stays in `<your Maya user folder>/<version>/scripts/site-packages`; delete its `numpy` folders there if nothing else uses it.

### Compatibility

| Maya | Python | Qt |
|---|---|---|
| 2022 | 3.7 | PySide2 |
| 2023 | 3.9 | PySide2 |
| 2024 | 3.10 | PySide2 |
| 2025, 2026 | 3.11 | PySide6 |

How it was tested:
- The measuring code is the Blender add-on's and gives the same results on the same meshes (shells, gaps, overlaps, tile crossings, texel density, arrows, info blocks). Stacks and the faster per-object code are checked against the plain code on the same scenes.
- The window, the UV tools and the 3D viewport colors are tested outside Maya, against a stand-in for Maya's commands, with Python 3.7, 3.9 and 3.11, NumPy 1.21 and 1.26, PySide2 5.15 and PySide6 6.5.
- The GPU view is compared with the standard view, pixel by pixel, on OpenGL 4.5 (compatibility and core profile), with its shaders for old OpenGL (GLSL 1.20), and on OpenGL ES 3.

That is not the same as running in your Maya. **Settings → Run Self-Test** checks the whole chain there in a few seconds: it builds three small test meshes, checks reading, measuring, stacks, selecting, every UV tool and the 3D colors, reports the result and deletes the meshes again. Run it once after installing; the Script Editor has the full report.

---

## Quick start

1. Click the **UV Gaps** shelf button (or run `import uvgap; uvgap.show()`).
2. Select your meshes. Their UV layout appears in the window, first the shells, then the gaps.
3. Set **Texture → Size** to the resolution you bake or paint at, or choose **UV Editor Image**.
4. Set **Minimal** and **Needed** to your padding targets.
5. Look for magenta **Overlap** labels: those are shells that lie on each other without fitting. Stacks of copies that fit show one block with `× N` instead.
6. To bake or pack with one copy of each part in the 0–1 tile, press **Move All but One** in *Overlaps and Stacks*.
7. To fix a shell, pick it in the view and use **Move** (W), **Rotate** (E) or **Scale** (R), or the buttons in *UV Tools*.
8. For texel density, tick **Texel Density** at the top of the window. Then pick a unit and set **Needed**. *Low* and *High* fill themselves from your shells while *Auto Low / High* is on.
9. To see the colors on the mesh, tick **In 3D Viewport**.
10. To work per material, use **Material Sets**: check sets, then **Select**, **Hide** or **Reveal**.

You can also edit UVs in Maya's UV Editor as usual; the window updates as you go.

---

## The UV Gaps window

The window has the settings and tools on the left and the UV view, with its toolbar, on the right. Drag the divider between them to resize; dock the window anywhere in Maya's layout.

**Top row**

| Control | What it does |
|---|---|
| Gaps | Measure and draw the pixel gaps between shells and to tile borders. |
| Texel Density | Tint every shell by its texel density and add the density to its info block. |
| In 3D Viewport | Color the shown meshes in the 3D viewport by the texel density of their shells. |
| Refresh | Read the meshes again. Normally not needed. |
| Settings | Save Settings as My Defaults, Reset to My Defaults, Reset to Factory Defaults, Draw the View on the GPU, Run Self-Test, About. |

**The toolbar above the view**

| Buttons | What they do |
|---|---|
| Select, Move, Rotate, Scale | The tool the left mouse button uses (Q, W, E, R). |
| UV, Edge, Face, Shell | What a click picks (1, 2, 3, 4). |
| Frame, All | Frame the selection (F) or all shells (A). |

**Navigating**

| Action | Result |
|---|---|
| Mouse wheel, or Alt + right-drag | Zoom, around the mouse pointer. |
| Middle-drag (with or without Alt) | Pan. |
| F | Frame the selected UVs (all shells when nothing is selected). |
| A | Frame all shells. |
| Home | Frame the 0–1 tile. |
| Right-click | Framing, and the UV wireframe and grid on or off. |

**Selecting**

| Action | Result |
|---|---|
| Click | Select the UV, edge, face or shell under the pointer in Maya. A click on nothing clears the selection. |
| Drag | Select with a rectangle: the UVs inside it, the edges and faces whose middle is inside it, the shells with a UV inside it. |
| Double-click | Select the whole shell, whatever is being picked. |
| Shift | Toggle. |
| Ctrl | Deselect. |
| Ctrl + Shift | Add. |

With *Stacked Shells as One* on, a click on a stack in Shell mode picks all its shells, so a stack moves as one.

**Moving, rotating, scaling.** With Move, Rotate or Scale chosen, a manipulator stands in the middle of the selected UVs:

| Tool | Drag | Result |
|---|---|---|
| Move (W) | the square in the middle | Move freely. Shift keeps to the nearer direction. |
| | an arrow | Move along U or V only. |
| Rotate (E) | anywhere inside the ring | Rotate around the pivot. Shift: steps of 15°. |
| Scale (R) | a handle | Scale along U or V: twice as far from the pivot is twice the size. |
| | the square in the middle | Scale both ways: 100 pixels to the right or up doubles, to the left or down halves. Shift: steps of 0.1. |

While you drag, the result is previewed in light blue, with the amount next to the manipulator; Maya's UVs change when you release the button. Esc cancels a drag. A click, or a drag beside the manipulator, selects as with the Select tool. Dragging works on selected components (UVs, edges, faces, shells picked in the view), not on meshes selected as whole objects.

**Keys.** They work while the mouse pointer is over the view.

| Key | |
|---|---|
| Q, W, E, R | Select, Move, Rotate, Scale. |
| 1, 2, 3, 4 | Pick UVs, edges, faces, shells. F9 to F12 work as in Maya: UVs, edges, faces, UVs. |
| F, A, Home | Frame the selection, all shells, the 0–1 tile. |
| Esc | Cancel the drag in progress. |

The line at the top of the view says how many meshes and shells are shown and at which texture size, and what is being computed. The line at the bottom names the tool and what a click picks.

**Which meshes are shown**
- meshes selected as objects, including the meshes under a selected group;
- meshes with selected components (UVs, faces, edges or vertices);
- meshes in component mode.

They are kept in the order of their names, whatever was selected first. Each mesh shows its current UV set, as in Maya's UV Editor.

**Selecting from the window.** The view, and the Select buttons, select components in Maya: UVs, edges or faces as chosen in the toolbar; for shells, their UVs, or their faces while faces are what you are selecting. The meshes shown in the window are put into component mode first, so they stay shown when the selection changes. Every selection can be undone.

**Drawing on the GPU.** The view is drawn with OpenGL, which keeps panning and zooming smooth with many faces. If OpenGL can't be used, the window switches to its standard view by itself and says so in the Script Editor. If the view stays black or misdraws, turn off **Settings → Draw the View on the GPU**. That choice is kept for your computer, not saved with scenes.

---

## UV tools

The *UV Tools* section works on what is selected in Maya, wherever you selected it: in the UV view, in Maya's UV Editor or in the 3D viewport. Each button is one undo step and uses Maya's own UV commands, so the results are the same as with Maya's tools.

| Control | What it does |
|---|---|
| Cut | Cut the UVs along the selected edges. With faces selected, cut them out along the border of the selection. With UVs selected, cut along the edges between them. |
| Weld | With edges selected, sew them. With UVs, faces or vertices selected, merge the selected UVs that belong to the same vertex, however far apart they are. |
| Weld Within | Merge UVs of the same vertex that are closer than the distance, in UV units or texture pixels: among the selected UVs, or all UVs of meshes selected as objects. |
| Move, with ← ↑ ↓ → | Move the selected UVs by the amount, in UV units or texture pixels. 1 UV is one UDIM tile. |
| Rotate, with ↺ ↻ | Rotate by the angle, counterclockwise or clockwise. |
| Scale, with × ÷ | Scale up or down by the factor. |
| Pivot | *Selection Center*: rotate and scale around the middle of everything selected. *Each Shell's Center*: every shell around its own middle. |
| Show Selection | Highlight the selected UVs, edges, faces and shells in the view. |

The Move, Rotate and Scale buttons also work on meshes selected as whole objects: all their UVs are transformed.

Welding never joins UVs of different vertices: UVs can only be merged where the mesh itself is connected. To bring two separate shells together, move them; to join shells along a shared mesh edge, select the edge and press Weld.

---

## Overlaps and stacks

Not every overlap is a mistake. When a model reuses a part, such as the bolts of a wheel or the left and right half of a symmetric object, the copies usually share the same piece of the texture: their shells have the same shape and lie exactly on each other. Reporting each pair of them as an overlap hides the real problems.

**Stacks.** With *Stacked Shells as One* on, shells that cover each other by at least the *Stack Match* (99.9 % by default) form a stack:
- A stack is measured as one shell. Gaps go from the stack to its neighbors, once.
- The shells of a stack are not overlaps of each other.
- A stack has one info block, with `× N` for the number of shells in it, for example `× 6` or `× 6 (2 flipped)`.
- A stack is tinted and outlined once. It shows as flipped only when all its shells are.
- When the shells of a stack differ in texel density, because one copy is larger in the scene, the block shows the span, for example `256 – 512 px/m`. When their objects have different scale, it says `Scale varies`.
- In Shell mode, a click picks the whole stack.

**Overlaps.** Everything else that lies on another shell is an overlap and gets the magenta crosses and **Overlap** labels where the borders cross or one shell's border runs inside the other:
- a copy that was placed carelessly and covers its original by less than the Stack Match (97 %, say),
- shells of different shape lying on each other,
- shells that overlap partly, by mistake or because packing failed.

A carelessly placed copy lying on a stack is one overlap, between it and the stack.

| Setting | Default | Description |
|---|---|---|
| Stacked Shells as One | on | Treat shells lying on each other as one shell. Off: every pair of them is an overlap, as in 1.0. |
| Stack Match | 99.9 % | How much two shells must cover each other to be a stack. Lower it to accept copies that are placed less exactly. |
| Select: Overlapping | — | Select the shells that overlap and aren't a stack, with all shells of the stacks involved. |
| Select: Stacked | — | Select every shell that lies in a stack. |
| Select: All but One | — | Select all shells lying on others except one of every stack and of every group of overlapping shells. |
| Move All but One, Tiles | 1 | Move those shells the given number of UDIM tiles along U (negative: to the left) and select them. One undo step. |
| Only in the 0-1 Tile | on | *All but One*, for selecting and for moving, takes only shells lying in the 0–1 tile (UDIM 1001). Copies you moved away earlier stay where they are. Off: shells in every tile. |

Shift-click a Select button to add to the selection.

**Which shell stays.** Of every stack, and of every group of overlapping shells, one shell is left where it is (and left out of the *All but One* selection):
- one that isn't flipped, where there is one;
- of a stack and a single shell lying on it, the stack's shell;
- then the larger one;
- then the first by the name of its object, so the same shell stays every time.

*Move All but One* moves the shells from where they are, so the copies of a stack arrive as a stack in the other tile. With *Only in the 0-1 Tile* on, they are left alone there: pressing the button again later only moves what has come to lie on other shells in the 0–1 tile since. With it off, shells in every tile are taken, and pressing the button again moves all but one of the moved copies a tile further. In a layout that already uses several tiles, set *Tiles* so that the shells land in an empty one.

With *Same Material Only* on, only shells of the same material form stacks and overlaps.

---

## Texel density

Texel density is how many texture pixels cover one meter of the model's surface. When it is even across a model, and across the models in a scene, textures look equally sharp everywhere. A shell with too little density looks blurry next to its neighbors; one with too much spends texture space that other shells could use.

The tool finds the density of every UV shell and shows it in two places: in the UV view, where you fix it, and on the model in the 3D viewport, where you see the result. Both use the same settings (*Texture*, *Unit*, *Low*, *Needed* and *High*) and the same colors.

**How the number is found.** The texture pixels a shell covers are divided by its surface area in square meters, and the square root is taken. A 1 × 1 m face whose UVs span a quarter of the width and height of a 2048 px texture covers 512 × 512 pixels, so its density is 512 px/m. The surface is measured in world space, so object scale counts, non-uniform scale too. Maya measures in centimeters internally whatever working units you choose, so the density doesn't depend on that setting.

**The colors.** Each shell is tinted by how its density compares with your values:

| Color | Density |
|---|---|
| Red | at or below *Low* |
| Yellow | between *Low* and *Needed* |
| Green | at *Needed*, your target |
| Cyan | between *Needed* and *High* |
| Blue | at or above *High* |
| Gray | the shell has no 3D area |

Red and yellow shells get fewer pixels than your target and will look softer; cyan and blue ones use more texture space than they need. *Fill Opacity* sets how strongly the colors cover the UV layout and the shaded model.

### In the UV view

Tick **Texel Density** at the top of the window.
- Every shown shell is tinted, and its info block gives its density in px/cm, px/m, px/in or px/ft.
- The values follow your edits: scale a shell and its color and number change with it.
- A stack is tinted by the shell that stands for it. Its block shows the span of densities when its shells differ.
- The **Texel Density** section lists the number of shells, their lowest, highest and median density, and how many are at or below Low and at or above High.
- With *Auto Low / High* on, red marks the least dense shells of the checked material sets and blue the densest, so the spread shows at a glance. Type your own Low and High to grade against fixed limits instead.
- **Texel Density Range** outlines the shells inside a range of densities and selects them with one click. For example, pull the right handle down until the least dense shells are outlined, select them, and scale them up until they turn green.
- The **Material Sets** list shows each material's density range, so a texture set that is out of line stands out.

### In the 3D viewport

Tick **In 3D Viewport** at the top of the window.
- Every face of the shown meshes takes the color of its UV shell, drawn over the shaded model, so you can see on the model itself where texture resolution falls short or is wasted. Every copy of a stacked part has its own color here.
- The colors follow your edits, and the meshes as you move or scale them.
- Changing Low, Needed, High, the unit, the texture size or the opacity recolors at once.
- The colors are drawn by a small helper node, `uvGapOverlay_TD`, that the window creates. It is hidden in the Outliner, can't be picked in the viewport and is never saved with your scene. Its plug-in, `uvGapOverlay.py`, loads by itself.
- The colors need Viewport 2.0 and stay while the window is open.

---

## Reading the overlay

| On screen | Meaning |
|---|---|
| Small square on a shell border, a colored line and a label like `12.3 px` | Gap from that point to the nearest other shell. The square marks the point being measured. |
| Colored line ending in a short tick | Distance from the shell to its tile border. The tick sits on the tile edge. |
| Magenta ✕ with **Overlap** | The point lies inside another shell, or two shell borders cross there. |
| Magenta ✕ with **Crosses tile** | The shell straddles a UDIM tile line. |
| Blue tint and blue outline | The shell's UVs are mirrored; its info block says **Flipped**. |
| Shell tinted red to blue | The shell's texel density. Gray: the shell has no 3D area. |
| Info block on a shell | One box per shell: its density (`512 px/m`), `Scale 1.5` in orange when its object's scale is not 1, **Flipped**, and an arrow on the left. |
| `× 4` in a block | A stack: four shells lie on each other here. `× 4 (1 flipped)`: one of them is mirrored. |
| `256 – 512 px/m`, `Scale varies` in a stack's block | The shells of the stack differ in texel density, or their objects in scale. |
| Green arrow in a block | Where the scene's up (+Y) runs across the shell. An arrow pointing up means the texture stands upright on the model. |
| Blue arrow in a block | The shell lies flat (a floor or a table top), so the arrow shows where −Z runs instead: away from the front view. |
| Orange dots, lines, tint or outline | The selected UVs, edges, faces and shells. |
| Light blue lines and dots | Where the selection will be when you release the mouse button. |
| White outline | The shells inside the Texel Density Range, while it is narrower than all shells. |
| Whole overlay dimmed | The shells or the gaps are being computed again. |

In a Z-up scene the arrows show +Z (blue), and +Y (green) on shells lying flat.

**Gap colors:** red = 0 px (touching) → yellow = Minimal → green = Needed or more. Tile-border distances use the same colors with the *Border Minimal / Needed* values.

**Texel density colors:** red at Low and below → yellow → green at Needed → cyan → blue at High and above. With Low 100, Needed 300 and High 500 px/m, a shell at 200 px/m is yellow and one at 400 px/m is cyan.

**Label priority:** tile crossings and overlaps come first, then the info blocks (those with a scale note, *Flipped* or a span of densities first), then the smallest distances relative to their target. With *Hide Overlapping Labels* on, less important labels that would cover them are skipped; zoom in to see them. Lines and colors are always drawn.

---

## Settings

Settings are saved with your scene when you save it. A new scene starts with your own defaults (**Settings → Save Settings as My Defaults**), or with the defaults below. The sections fold open and closed with their titles.

### Summary

The number of shown meshes, shells and faces; how many gaps were measured and how many are below Minimal; the smallest gap; overlapping shell pairs, shells crossing a tile border, flipped shells and stacks. The gray line below says how long the last update took: reading the meshes from Maya, finding the shells, measuring the gaps, and drawing the view. When only some objects or material sets had to be done again, it says how many.

### UV Tools

See [UV tools](#uv-tools).

| Setting | Default | Description |
|---|---|---|
| Weld Within | 1 px | The distance for *Weld Within*, in texture pixels or UV units. |
| Move | 1 UV | The amount the arrow buttons move by, in UV units or texture pixels. |
| Rotate | 90° | The angle of the rotate buttons. |
| Scale | 2 | The factor of the scale buttons. |
| Pivot | Selection Center | What rotating and scaling turn around. |
| Show Selection | on | Highlight what is selected in the view. |

### Texture

| Setting | Default | Description |
|---|---|---|
| Size | 2048 | Texture resolution used to convert UV distances to pixels. Options: 256–8192, **Custom** (separate width × height; non-square textures are supported), or **UV Editor Image** (the size of the image shown in Maya's UV Editor, falling back to the custom width × height when there is none). |

### Gaps

| Setting | Default | Description |
|---|---|---|
| Minimal | 4 px | Smallest acceptable gap between shells (yellow). |
| Needed | 8 px | Target gap between shells (green). |
| Search Radius | 32 px | Only gaps up to this size are measured; shells farther apart are not treated as neighbors. Also decides which shells count as "near" a tile border. |
| Points per Shell | 24 | Measurement points spread evenly along each shell's whole border, outer border and holes included. |
| Shift Along Border | 0 % | Slides every point along the border. 0–100 % covers one full step between neighboring points, so sweeping it checks the entire border. |
| Selected Shells Only | off | Measure only from shells with selected components. Distances still go to every shell. Meshes selected as whole objects count fully; meshes in component mode with nothing selected don't count. A stack counts when any of its shells is selected. |
| Same Material Only | on | Measure gaps and find overlaps and stacks only between shells of the same material. Shells of other materials are ignored, including as obstacles between a shell and its tile border. |

### Overlaps and Stacks

See [Overlaps and stacks](#overlaps-and-stacks).

### UDIM Tiles

| Setting | Default | Description |
|---|---|---|
| Measure to Tile Borders | on | Measure shells near the border of their own tile, flag shells crossing a tile line, and measure gaps only between shells in the same tile. |
| Border Minimal | 2 px | Smallest acceptable distance from a shell to its tile border. |
| Border Needed | 4 px | Target distance from a shell to its tile border. |

### Flipped Shells

| Setting | Default | Description |
|---|---|---|
| Show Flipped Shells | on | Highlight mirrored shells. |
| Select Flipped Shells | — | Select all mirrored shells, those inside stacks too. Shift-click adds to the selection. |

### Material Sets

A list of the materials used by the shown meshes. A face's material is the surface shader of its shading group; faces without a shading group form the **(No Material)** set.

Each row shows:
- a checkbox,
- the material,
- the number of shells shown and their texel density range, in the current unit,
- **Shown**: untick it to hide that set's shells.

Double-click a row to select its shells.

| Button | What it does |
|---|---|
| Select | Selects the shells of the checked sets, or of the highlighted row when nothing is checked. Shift-click adds to the selection. |
| Hide | Hides the shells of the checked sets (or the highlighted row) from the window, the measurements and the 3D viewport colors. Maya's own display is not changed. |
| Reveal | Shows them again. |
| Check: All / None / Invert | Changes which sets are checked. |

The checked sets also decide what *Auto Low / High* fills from.

### Texel Density

| Setting | Default | Description |
|---|---|---|
| Unit | px/m | px/cm, px/m, px/in or px/ft. Switching the unit converts the values below; the densities they stand for don't change. |
| Needed | 300 px/m | The density you aim for. Shells at this density are green. |
| Low | 100 px/m | Shells at or below this density are red. Typing a value turns *Auto Low / High* off. |
| High | 500 px/m | Shells at or above this density are blue. Typing a value turns *Auto Low / High* off. |
| Auto Low / High | on | Fills Low and High with the lowest and highest density of the checked material sets, or of all shells when none is checked. See [Auto Low / High](#auto-low--high). |
| Fill Now | — | Fills Low and High once, whether Auto is on or not. |
| Fill Opacity | 0.35 | Opacity of the density colors, in the UV view and the 3D viewport. |

Below the settings: the number of shells, the lowest, highest and median density, and how many shells are at or below Low and at or above High.

### Texel Density Range

| Setting | Default | Description |
|---|---|---|
| Range | 0–100 % | Two handles, as a share of the way from the lowest (0 %) to the highest (100 %) density among the shown shells. Drag a handle, or the band between them; double-click for the whole range. Below it are the densities the handles stand for and how many shells are in range. |
| Highlight Range | on | Outline the shells in range in white while the range is narrower than all shells. |
| Select Shells in Range | — | Selects the shown shells whose density is in range. Shift-click adds to the selection. |

### Shell Info

| Setting | Default | Description |
|---|---|---|
| Info Blocks | on | Show the extra lines and the arrow in the info blocks. |
| Orientation Arrows | off | Add an arrow to each shell's block (see [Reading the overlay](#reading-the-overlay)). Arrows also show on their own, with the other overlays off. |
| Object Scale | on | Add `Scale …` to the blocks of shells whose object's scale is not 1, for example `Scale 1.5` or `Scale 1 × 2 × 1`. The note joins the blocks while the gap or texel density overlay is on. |

The texel density line comes with *Texel Density* and *Flipped* with *Gaps*.

### Display

| Setting | Default | Description |
|---|---|---|
| Font Size | 12 px | Label size. |
| Opacity | 0.9 | Opacity of lines, markers, labels and arrows. |
| Line Width | 2 px | Width of the measurement lines. |
| Label Background | on | Dark box behind each label and info block. When off, text is drawn with a shadow instead. |
| Hide Overlapping Labels | on | Skip labels that would cover a more important one. |
| UV Wireframe | on | Draw every UV edge, not only the shell borders. |
| Grid | on | Draw the UDIM tile grid and a finer 0.1 grid. |

### Colors

*Overlap*, *Touching (0 px)*, *At Minimal*, *At Needed* and *Flipped* are all editable.

---

## How it measures

**Shells.** Faces that share a UV belong to one shell, as in Maya's UV Editor. UVs that only lie on the same spot without being merged (not sewn) don't join shells. A shell's border is made of the UV edges used by only one face, ordered into continuous loops, holes included. Faces without UVs in the current UV set are left out.

**Points and gaps.** Points are spaced evenly by length along each shell's whole border. From each point, the tool finds the closest point on another shell's border, measured in texture pixels, so non-square textures are handled correctly. Only neighbors in front of the border count, within about 75° of the direction the border faces. A measurement therefore never passes through the shell's own body or runs sideways along its edge. Neighbors beyond the Search Radius are ignored. Touching shells read **0.0 px**.

**Stacks.** Two shells are a stack when each covers the other by at least the Stack Match: their areas are compared, and the area between their borders is measured. Shells with the same border are matched directly; shells whose borders differ a little, because a copy was moved by a hair or has other vertices along the same outline, are compared by the distance between their borders. Stacks join up: if A matches B and B matches C, all three are one stack. The shell standing for a stack in the measurements is its first unflipped shell.

**Overlaps.** A point is an overlap when it lies inside another shell. The inside test uses the even-odd rule, so holes count as empty space. Independently, every pair of shells is checked for crossing borders and for one shell lying inside or on another. That way an overlap is reported even when no point happens to land on it. Shells that only touch are not overlaps, and the shells of one stack are not overlaps of each other.

**Tile borders.** Tile borders are the integer UV lines; the 0–1 square is tile 1001. Each shell belongs to the tile that contains the center of its bounding box. A point measures to an edge of its own tile only when:
- its border faces that edge,
- the edge is within the Search Radius, and
- no shell lies in between. This includes other shells and the shell's own body, such as the far side of a ring.

Shells whose bounds span a tile line are reported as **Crosses tile** instead of being measured.

**Flipped shells.** A shell is flipped when its total signed UV area is negative. Its faces run clockwise in UV space, which means the texture appears mirrored on the model.

**Texel density.** For each shell, texel density = √(texture pixels the shell covers ÷ its surface area in square meters).
- Pixels covered = UV area × Width × Height. For a non-square texture the result is the geometric mean of the horizontal and vertical density.
- The surface area is taken in world space, so object scale is included, non-uniform scale too.
- The info block sits on the face nearest the shell's center of area, so it stays on the shell even for rings and L-shapes.

<a id="material-sets"></a>**Material sets.** A shell belongs to the material that covers most of its UV area. That matters only for the rare shell whose faces use several materials.
- *Same Material Only* compares each shell only with shells of its own material. With tile borders on, a shell's neighbors must share both its tile and its material.
- Select, Hide and Reveal act on whole shells, by that material.
- Shading groups using the same material count as one material set.

<a id="auto-low--high"></a>**Auto Low / High.** While it is on and texel density is shown (in the UV view or the 3D viewport), Low and High are filled with the lowest and highest density of the checked material sets (all shells when none is checked):
- when you turn it on,
- when you check or uncheck a set,
- when other meshes are shown,
- when the texture size changes, since every density changes with it,
- after Undo or Redo.

They are not refilled while you edit UVs, so the colors stay put while you work. Press **Fill Now** to refill after edits. Typing a value into Low or High turns Auto off, so what you typed stays.

**Orientation arrows.** For each face, the tool fits how its UVs map onto its 3D surface. The scene's up axis, projected onto the face, is then taken back into UV space. Faces are weighted by their area and by how much of the axis lies along them. The result is averaged over the shell.
- When up runs mostly across the shell (a wall, a slope), the arrow shows up (+Y in a Y-up scene, green).
- On shells lying flat (a floor, a table top), up points out of the surface, so the arrow shows where −Z runs instead (blue). In a Z-up scene: +Z (blue), and +Y (green) on flat shells.
- A shell with neither direction clear gets no arrow; a dome seen from above is an example.

---

## Choosing padding values

Bake margin (dilation) grows outward from every shell. Two neighboring shells each need room for their own margin, so the gap between them should be about **twice** the margin. A shell needs only **one** margin to the tile border. That is why the border defaults (2 / 4 px) are half the shell defaults (4 / 8 px).

If you rely on mipmaps, each mip level halves the distance in pixels, so textures seen from far away need more padding.

---

## Performance

How work is saved:
- **In the background.** Shells are found and gaps measured in a second thread. Maya and the window stay responsive, the UV layout appears as soon as the shells are known, and the gaps follow.
- **Per object.** A mesh is read from Maya only after it changed, and only that mesh's shells are found again. Many small meshes are processed together in one pass. When Maya reports a change that changed nothing (it does so for many reasons), the mesh is read, found the same, and nothing is computed.
- **Per material.** With *Same Material Only* on, each material is measured on its own, and only the materials an edit touched are measured again. Hiding a material set measures nothing again.
- **Stacks.** A stack is measured once, and no crossing points are searched between its shells.
- **Only what is needed.** Moving an object, or its points in the scene, keeps its gaps and only updates its texel density. Selecting reads the selection and nothing else. Arrows, info blocks, colors, thresholds, the unit and the display settings only redraw.
- **On the GPU.** The layout, the density tint, the gap lines and the selection are kept in GPU buffers and only redrawn when you pan or zoom.

Edits made with the window's own tools are read back at once. For edits made elsewhere in Maya, a heavy layout waits until you pause for ~0.25 s and dims the overlay meanwhile.

The tool's own work, measured outside Maya with the default settings and texel density on (Python 3.11, NumPy 1.26). Reading the meshes from Maya comes on top and grows with the number of meshes and their size.

| Layout | Find shells | Measure gaps | Redraw while panning, standard view |
|---|---|---|---|
| 1 mesh, 144 shells, 1.3k faces | ~5 ms | ~15 ms | ~20 ms |
| 1 mesh, 1,600 shells, 14k faces | ~40 ms | ~0.2 s | ~75 ms |
| 1 mesh, 1,600 shells, 102k faces | ~0.23 s | ~0.25 s | ~85 ms |
| 630 objects, 4,000 shells, 24k faces, parts reused (665 stacks) | ~0.13 s | ~0.09 s | ~90 ms |
| 1,300 objects, 8,200 shells, 49k faces, parts reused (1,430 stacks) | ~0.25 s | ~0.2 s | ~0.2 s |
| the same, *Stacked Shells as One* off (70,000 overlapping pairs) | ~0.25 s | ~1.7 s | ~0.23 s |
| 10 meshes, 16,000 shells, 1,000k faces | ~5 s | ~3 s | ~0.75 s |

For comparison, version 1.0 needed about 9 s for the 1,300-object layout on the same computer, and did all of it again for most changes.

In the GPU view, a redraw while panning costs the computer about 10 ms for the 1,300-object layout (laying out the labels); the graphics card does the rest. The numbers in the table are for the standard view, which draws everything on the CPU.

On the same 1,300-object layout, with everything shown and measured:

| Change | Work done again |
|---|---|
| Hide a material set | shells of the objects using it: ~0.1 s |
| Reveal it | the same, and its gaps: ~0.2 s |
| Orientation arrows, info blocks, colors | none |
| Select a shell | none |
| Move one shell | that object's shells and its material's gaps |

For very large layouts, lower *Points per Shell*: the gap measurement grows with points × shells. *Selected Shells Only* also helps while you work on part of the layout.

---

## Limitations

- Maya's own UV Editor shows nothing extra: the overlay is in the UV Gaps window's view.
- The UV tools are the basic ones. For unfolding, packing, straightening and the like, use Maya's UV Toolkit; the window follows.
- Welding merges UVs only where they belong to the same vertex (Maya's Merge UVs and Sew).
- Dragging the manipulator needs selected components. Meshes selected as whole objects are transformed with the buttons.
- A click on shells lying on each other picks the smallest face under the pointer, and of equal ones the first by object name. Use a rectangle, or the Select buttons of *Overlaps and Stacks*, for the others.
- Two shells are a stack only when they cover each other by the Stack Match. A part reused at another size or rotated in UV space is not a stack with its original.
- *Move All but One* moves shells by whole tiles along U from where they are. It doesn't look for an empty tile.
- A shell belongs to the tile that holds the middle of its bounds; *Only in the 0-1 Tile* goes by that.
- The 3D viewport colors are shown while the window is open, in Viewport 2.0. Isolate Select hides them, because their helper node is not part of the isolated set.
- Hiding a material set hides its shells in the window and the 3D colors only.
- Gaps are sampled at the measurement points. A narrow spot between two points is found by adding points or sweeping *Shift Along Border*. Overlaps are always found.
- Each point reports only its nearest neighbor.
- UVs meant to wrap around (tiling textures) are not treated as wrapping. With tile borders on, every tile is a separate texture.
- Flipped detection works per shell, based on its overall orientation. Individual folded faces inside an otherwise normal shell are not flagged.
- Texel density is an average per shell. Stretching inside a shell, where one part is denser than another, is not shown.
- Orientation arrows show a shell's average direction. On strongly curved shells, such as a cylinder unwrapped around its axis, the direction changes across the shell.
- A shell whose faces use several materials counts as the material covering most of it.
- Only the current UV set of each mesh is measured.
- The 3D helper node uses a node type id from the range Autodesk leaves for in-house tools (`0x0007E5A1`). The node is never saved, so if another plug-in in your Maya uses the same id, only the 3D colors are affected: the plug-in won't load, and Maya shows a warning that the 3D overlay is not available. Change `NODE_ID` in `plug-ins/uvGapOverlay.py` to another id in that range if this happens.

---

## Troubleshooting

- **The view says "Select meshes…".** Select a mesh, a group, or components of a mesh. Objects in component mode count too.
- **"No UVs on the shown faces".** The mesh's current UV set is empty. Pick another UV set in the UV Editor.
- **Numbers look too small or too large.** Check *Texture → Size*: distances and densities scale with the resolution.
- **The view is black, flickers or misdraws.** Turn off **Settings → Draw the View on the GPU**. If the window can't be used at all, set the environment variable `UVGAP_NO_GPU=1` before starting Maya, or run `import os; os.environ["UVGAP_NO_GPU"] = "1"` in the Script Editor before opening the window.
- **It is slow.** The gray line under the Summary says where the time goes. *read* is Maya handing over the meshes, *shells* and *gaps* are the tool's own work, *draw* is one redraw of the view. Please include that line when you report a slow scene.
- **Copies I stacked are reported as overlaps.** They cover each other by less than the *Stack Match*. Lower it (99 %, say), or select them with *Overlapping* and fit them onto each other. When there are many overlaps, the *Overlaps and Stacks* section says so.
- **A click doesn't select.** While the layout is dimmed, the shells are being read again and clicks wait for them. If nothing is dimmed, check the toolbar: a click picks what is chosen there (UV, Edge, Face, Shell).
- **The keys Q W E R don't switch the tool.** Move the mouse pointer over the view first; while a number is being typed in the window, the keys go to that field.
- **Rotate turns the wrong way, or a tool does something unexpected.** Run **Settings → Run Self-Test**: it checks what every tool does in your Maya and names what differs.
- **Low and High keep changing.** *Auto Low / High* is on and the checked material sets, the shown meshes or the texture size changed. Type a value, or untick *Auto Low / High*, to keep your own values.
- **No colors in the 3D viewport.** Check that *In 3D Viewport* is ticked, the viewport uses Viewport 2.0 and Isolate Select is off. If the Script Editor says the 3D overlay is not available, open *Windows → Settings/Preferences → Plug-in Manager* and look for `uvGapOverlay.py`.
- **The NumPy prompt comes back every time.** NumPy went somewhere this Maya's Python doesn't look. Run the command under [Installation](#installation) for your Maya version and restart Maya.
- **The window is empty after restarting Maya.** If Maya restored the docked window before the tool could load, close it and open it again with the shelf button.
- **Something went wrong.** The tool prints each error once to the Script Editor, and the window's status line says so. **Settings → Run Self-Test** checks the whole chain in your Maya and prints a report.

---

## Changelog

**1.1.0**
- Speed. Shells and gaps are computed in a background thread, object by object and material by material; the layout shows before the gaps are measured; only what an edit touched is computed again. Many small meshes are processed in one pass. A 1,300-object layout that took about 9 s now takes about half a second, plus Maya's time to hand over the meshes.
- Hiding or revealing a material set, the orientation arrows, the info blocks and selecting no longer measure anything again.
- Stacks: shells lying on each other on purpose are measured as one shell and are not reported as overlaps (*Stacked Shells as One*, *Stack Match*). Info blocks show `× N`, the span of texel densities and `Scale varies`.
- *Overlaps and Stacks*: select overlapping shells, stacked shells, or all but one of each; **Move All but One** to another UDIM tile, keeping an unflipped shell.
- UV tools: pick UVs, edges, faces and shells in the view (click, rectangle, double-click); Cut, Weld, Weld Within; Move, Rotate, Scale with a manipulator or by amounts; pivot at the selection's or each shell's center.
- The UV view is drawn with OpenGL, with the previous view as a fallback.
- A timing line in the Summary.
- The shown meshes are kept in the order of their names.
- The self-test also checks stacks and every UV tool.
- A mesh that takes the place of a deleted one of the same name is read and followed.

**1.0.0**
- First Maya version, with the features of the Blender add-on 1.4.0: pixel gaps between shells and to UDIM tile borders, overlaps, tile crossings and flipped shells; texel density in the UV view and the 3D viewport; info blocks with orientation arrows and object scale; material sets with select, hide and reveal; Auto Low / High; selection by texel density range.
- The UV Gaps window: a dockable UV view of the selected meshes that follows your edits, with click-to-select.
- Drag-and-drop installer that also installs NumPy for Maya's Python.

---

## License

GPL-3.0-or-later
