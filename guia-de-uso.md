# Rackview 3D — User guide

3D rack visual for Power BI. Each storage position becomes a colored pallet inside the rack structure,
and you navigate it with the mouse. This guide shows how to use it; no coding is needed.

**Contents**

1. [Getting started](#1-getting-started)
2. [Positioning: automatic grid or floor plan](#2-positioning-automatic-grid-or-floor-plan)
3. [Warehouses: walls, floor and height](#3-warehouses-walls-floor-and-height)
4. [Zones](#4-zones)
5. [Colors and legend](#5-colors-and-legend)
6. [Filtering without losing the structure (Show field)](#6-filtering-without-losing-the-structure-show-field)
7. [Navigating in 3D](#7-navigating-in-3d)
8. [Format pane summary](#8-format-pane-summary)
9. [Limits and common problems](#9-limits-and-common-problems)

---

## 1. Getting started

**One row per position.** Each Aisle + Rack + Level combination must appear **once**. If your data has
several rows per position (several materials on the same pallet), aggregate them first in Power Query.

| Aisle | Rack | Level | Quantity | Counted |
|---|---|---|---|---|
| 1 | 01 | A | 36 | Yes |
| 1 | 01 | B | 0 | No |
| 1 | 02 | A | 51 | Yes |

**Bring every position from your master data**, including the empty ones. The visual only draws what it
receives: if you filter the data, the structure shrinks. To filter without that, use the **Show** field
(section 6).

**Drag the fields into the visual:**

| Field | Required | What it is for |
|---|---|---|
| **Aisle**, **Rack**, **Level** | Yes | Where each pallet sits |
| **Color by** | No | Field that defines the color (quantity, counted, material…) |
| **Color (hex)** | No | Column with ready-made `#RRGGBB` colors; overrides color by value |
| **Show** | No | Measure or 0/1 column that hides pallets (section 6) |
| **X coord**, **Y coord** | No | Position of each rack on the floor plan (section 2) |
| **Warehouse** | No | Walls and floor per warehouse (section 3) |
| **Warehouse height** | No | Rack height of each warehouse (section 3) |
| **Occupancy**, **Capacity** | No | Occupied and total capacity of each position, in the same unit (section 3) |
| **Tooltip / details** | No | Fields shown when you hover a pallet |

Use a single field in each slot, except **Tooltip / details**. Aisle, Rack and Level can be text or
numbers; rows where any of them is empty are ignored and counted in the legend warning.

---

## 2. Positioning: automatic grid or floor plan

The visual has two modes, chosen by the fields: **without** X/Y coord it is the automatic grid; **with**
X coord and Y coord it is the floor plan.

### Automatic grid (Aisle, Rack and Level)

The visual lays everything out by itself: aisles sit one behind the other, the rack sets the position
along the aisle and the level sets the height.

Options under **Layout**:

| Option | What it does |
|---|---|
| Group aisles in pairs | Places aisles back to back two by two, with a corridor between pairs |
| Aisle pairing | **By position in the list** (1st with 2nd…) or **By aisle number** (1 with 2, 3 with 4…) |
| First aisle is the 2nd of a pair | Fixes the pairing when the first displayed aisle is the second of a physical pair |
| Pairs start at an even aisle | With pairing by number, forms 2-3, 4-5… |
| Aisle labels | Shows "Aisle N" at the end of each aisle |
| Level order | **Natural** (numbers before letters) or **Custom** (you type `A,B,C,1,2`) |
| Invert levels / racks / aisles | Reverses each axis (inverted racks align the aisles on the opposite side) |

Under **Geometry**: **Level height** (0.8 to 4) and **Corridor width** (1 to 12).

Each aisle has the size of its own data: an aisle with 12 racks and 3 levels is smaller than one with 45
racks and 8 levels.

### Floor plan (X coord and Y coord)

Use it when warehouses have different shapes, orientations or positions. Each rack gets a coordinate on a
single plane, and the visual draws exactly there.

**How to fill it in**

- Put **X coord** and **Y coord** (column or measure) in the positions table. The value belongs to the
  **rack** and repeats on every level of it.
- The unit is **1 rack**: neighboring racks have neighboring coordinates (X=10, X=11…).
- **Corridors are gaps**: leave one or more coordinates without a rack to open a corridor.
- **An aisle can be horizontal or vertical**: the visual detects it from how the coordinates vary.
- Y grows downward in the top view. If the drawing came out mirrored, multiply X (or Y) by -1 in the
  data.
- **Aisles with two rows** (corridor in the middle, U or zigzag numbering) work: just give the coordinate
  of each rack.

**Options that apply to the floor plan**

| Option | What it does |
|---|---|
| Geometry → Rack cell size | Size of each rack in the drawing (0.8 to 3) |
| Geometry → Level height | Height of each level (0.8 to 4) |
| Layout → Aisle labels | Turns the labels on and off |
| Layout → Aisle label | **Per lane**: one label per aisle, between the rows. **Per rack row**: one on each row |
| Layout → Level order / Invert levels | Same as the automatic grid |

On the floor plan, pairing, corridor width and inverting racks/aisles **do not apply**: the coordinates
already define them.

**Legend warnings**

- *Rack without coordinates:* not drawn.
- *Conflicting coordinates:* the same rack with more than one coordinate; the first row wins.
- *Overlap:* two racks in the same cell.

An aisle with **a single rack** is drawn horizontally, because there is no way to know its direction.

---

## 3. Warehouses: walls, floor and height

### Walls and floor per warehouse

Add the **Warehouse** field (with X coord and Y coord). The visual draws a **low wall** around each
warehouse and a **colored floor** per warehouse. Where two warehouses touch, they share the wall.

Under **Warehouses**:

| Option | What it does |
|---|---|
| Show walls / Wall color | Turns the walls on and off and picks the color |
| Wall height | Height of the walls (0.2 to 3) |
| Wall margin | Gap between racks and wall, in cells (0 to 6). Increase it until two neighboring warehouses share a wall |
| Floor colored by warehouse | Turns the floor on and off |
| Show warehouse labels | Turns the warehouse name label on and off |

The outline is always a **rectangle** around the warehouse racks.

### Warehouse label and occupancy

With the **Warehouse** field (floor plan), a label such as **Warehouse G1** floats above the center of each
warehouse, over the racks.

To show how full each warehouse is, add two fields, both **per position** and in the **same unit** (kg,
pallets, anything):

- **Capacity:** how much the position holds. It comes from your coordinates table.
- **Occupancy:** how much it holds now. Positions with no value count as zero.

The label then reads **Warehouse G1 · 72.43%**, and the legend gets an **Occupancy** line:
`72.43% (1,440 / 2,000)`; with more than one warehouse, one line per warehouse.

- Occupancy % = sum of Occupancy ÷ sum of Capacity, over **all** positions (including empty ones and
  those hidden by the **Show** field). Racks without a coordinate on the floor plan are left out.
- It only appears when the **Occupancy** field has at least one value. Without **Capacity**, the legend
  shows only the occupied total and the label shows only the name.
- If **Color by** is the warehouse (each color value is exactly one warehouse), each legend row also
  gets its occupancy: `G1 (120 · 15.0% · 72.43% occ.)`.
- Keep one row per position: duplicated rows would be ignored, only the first one counts.

### Warehouse height

Put a column with each warehouse height (for example 7, 9, 9) in the **Warehouse height** field. The
value can repeat on every row of the warehouse.

- All racks of the warehouse get **that height**, and the height of each level adjusts to the number of
  levels. So a 9-level warehouse gets short levels and a 4-level one gets tall levels, all with the same
  total height.
- The unit is the scene unit (close to meters).
- If the height is too small for the levels, the visual uses the minimum possible and warns in the legend.
- Without the field, the **Level height** from the Geometry card applies.
- Without the Warehouse field, the value applies to all racks.

---

## 4. Zones

Marks areas that are not racks, such as a dock, returns or an office. **Floor plan** only.

1. Under **Zones (max 20)**, turn on **Enable zones**.
2. Under **Number of zones**, choose how many you want (up to 20).
3. Fill in each **Zone N**:
   - **Title:** floats above the zone.
   - **X1, Y1** and **X2, Y2:** two opposite corners, in the same coordinates as the floor plan. The
     rectangle covers the whole cells between them.
   - **Color:** color of the floor and outline.
   - **Height:** 0 draws only the floor; above 0 it becomes a low volume, like a raised dock.
4. **Show zone titles** turns the titles on and off.

If you reduce the number of zones, the data of the hidden ones is kept. A zone with no title and all
zeros is ignored.

---

## 5. Colors and legend

Put a field in **Color by** and open **Format → Colors**. The pane adapts to the field type.

| Mode | When to use | What it does |
|---|---|---|
| **Automatic** (default) | Always | Picks by field type: numbers become a gradient; text, yes/no and dates become categorical |
| **Categorical** | Few distinct values | One color per value. Colors are stored per value. Up to 30 values; the rest turn gray ("Others") |
| **Gradient** | Numeric field | Minimum, maximum and, optionally, middle color. Automatic or typed minimum and maximum. Zero counts as "no value" |
| **Ranges** | Numeric field | 2 to 6 ranges, each with a limit and a color. The limit belongs to its range; the last range is open |

Other options:

- **No value:** color for rows where the field is empty.
- **Default pallet:** color of everything when there is no "Color by".
- **Color (hex):** if you compute the color in Power Query or in a measure, the `#RRGGBB` result
  overrides any rule.
- **Hide values:** list of values whose pallets disappear (the structure stays). Separate with commas:
  `No, 0, (No value)`. For numbers, use `>30`, `<=2`. With a decimal comma, separate with semicolons.

**Legend:** shows each value or range with its color and count (and %, if you want), considering only
what is visible. Under **Legend** you choose show, position, font, count and %. Warnings also appear
there. With the **Occupancy** field, it also shows the occupancy (section 3).

---

## 6. Filtering without losing the structure (Show field)

A regular slicer filters the data and the rack structure shrinks. To filter and **keep the racks drawn**,
hiding only the pallets, use the **Show** field: the slicer feeds a measure that says, per position,
whether it appears (1) or not (0).

**Step by step (example with Material)**

1. Create a **table just for the slicer**, with no relationship to the data (Modeling → New table):

   ```dax
   SlicerMaterial = DISTINCT ( Positions[Material] )
   ```

2. Create the slicer with the field `SlicerMaterial[Material]`.
3. Create the measure:

   ```dax
   Visible =
   VAR ok =
       NOT ISFILTERED ( SlicerMaterial[Material] )
           || SELECTEDVALUE ( Positions[Material] ) IN VALUES ( SlicerMaterial[Material] )
   RETURN
       IF ( ok, 1, 0 )
   ```

4. Drag `Visible` into the **Show** field of the visual.

With no selection, everything appears. With a selection, only the chosen pallets stay visible.

**Several filters:** create a table and a slicer for each filter and combine them in the same measure
with `&&`. A pallet only appears if it passes all of them.

**Position with several items in the same text** (for example "ITEM1, ITEM2") and a filter by storage
location:

```dax
Visible =
VAR sep = ", "
VAR positionItems = sep & SELECTEDVALUE ( Positions[Items in Position] ) & sep
VAR okItem =
    NOT ISFILTERED ( SlicerItem[Item] )
        || COUNTROWS (
            FILTER (
                VALUES ( SlicerItem[Item] ),
                CONTAINSSTRING ( positionItems, sep & SlicerItem[Item] & sep )
            )
        ) > 0
VAR okLocation =
    NOT ISFILTERED ( SlicerLocation[Location] )
        || SELECTEDVALUE ( Positions[Location] ) IN VALUES ( SlicerLocation[Location] )
RETURN
    IF ( okItem && okLocation, 1, 0 )
```

The separator (`sep`) must match the one used when concatenating. It prevents `ITEM1` from matching
`ITEM10`. With several items selected, the position appears if it has **any** of them.

**What the Show field understands**

| Value | Result |
|---|---|
| `1`, True, `Yes`, any non-zero number | Visible |
| `0`, False, `No`, empty text, blank | Hidden |
| Show field left empty | Everything visible |

> The measure must return `0` explicitly (`IF ( ok, 1, 0 )`). If it returns blank, Power BI may drop the
> row.

---

## 7. Navigating in 3D

| Action | How |
|---|---|
| Rotate | Drag with the left button |
| Zoom in / out | Mouse wheel, or the **+** and **−** buttons |
| Pan | Middle button, or **Shift** + drag |
| Reframe | **Reframe** button (top right corner) |
| See details | Hover a pallet |
| Select | Click a pallet; **Ctrl** + click selects several; clicking empty space clears |
| Power BI menu | Right button |

**Toolbar (top right corner):**

| Control | What it does |
|---|---|
| **Search…** | Type an address, SKU or any text and press **Enter**. It looks in every text field of the row (aisle, rack, level, color field, tooltip fields), ignoring case and accents; all the words must match. Matches stay bright, the rest fades, and the camera flies to them. Shows "N found" or "no match"; clear the box to remove the highlight. Coordinates, height, occupancy, capacity and **Show** are not searched |
| **All levels / Level X** | Shows only one level (pallets, empty slots and beams of the others disappear; the uprights stay). Only appears when there is more than one level |
| **Labels ▾** | Opens a menu with **Aisle**, **Zone** and **Warehouse** to turn each kind of label on or off |
| **+**, **−**, **Reframe**, **Drive** | Zoom, fit the whole scene, forklift mode |

The **Labels** menu, the search and the level choice last while the report is open and are not saved. A
label only shows when it is on in the menu **and** in the format pane (Aisle labels, Show zone titles,
Show warehouse labels).

**Empty positions.** Positions with no pallet (hidden by **Show** or by *Hide values*) appear as a faint
gray ghost box, so you can see where there is room. Turn it off under **Scene → Show empty positions
(ghost)**. Ghosts cannot be clicked and have no tooltip.

Selecting a pallet filters the other visuals on the page and fades the rest. The camera **does not move
by itself** when you filter; to reframe when the dimensions change, turn on **Camera → Reframe when
dimensions change**.

### Driving a forklift (game mode)

Click **Drive** (top right corner) to enter the warehouse on a forklift, with the camera in the cab. The
forklift starts in an aisle near the center and **cannot go through racks**. It can pass under racks
whose lowest beam is above its height (for example, racks without the bottom levels).

| Action | Key |
|---|---|
| Accelerate / reverse | **W** / **S** (or arrow keys) |
| Steer | **A** / **D** (only turns while moving) |
| Raise / lower the forks | **R** / **F** |
| Faster | **Shift** |
| Brake | **Space** |
| Look around | Drag the mouse; **C** recenters |
| Switch cockpit / 3rd person | **V** |
| Exit | **Esc** or the **Exit** button |

Aim the crosshair (screen center) at a pallet, up to 14 m away, to see the tooltip; click to select it.
Click the visual first so it receives the keyboard.

---

## 8. Format pane summary

| Card | What it has |
|---|---|
| **Colors** | Mode, colors per value/gradient/ranges, No value, Default pallet, Hide values |
| **Legend** | Show, position, font, count, % |
| **Layout** | Pairs, pairing, aisle labels (and type on the floor plan), level order, invert |
| **Geometry** | Level height, corridor width (grid) or cell size (floor plan) |
| **Warehouses** | Walls, color, height, margin, floor and labels per warehouse (floor plan with Warehouse field) |
| **Zones (max 20)** | Zones with title, corners, color and height (floor plan) |
| **Scene** | Background, floor (grid or solid) and its color, fog, upright and beam colors, beam opacity, empty positions (ghost) |
| **Camera** | Reframe when dimensions change |
| **Tooltip** | Show and include the "Color by" field |
| **Performance** | Resolution, reduce while moving, show beams, diagnostics (fps) |

On a weak machine, start with **Maximum resolution → Light** and **Show beams** turned off.

---

## 9. Limits and common problems

- Up to **30,000 rows** are loaded; above that the legend warns "data truncated".
- **Duplicate** positions use the first row and raise a warning.
- The visual was tested with more than 12,000 positions, runs offline and loads nothing from the
  internet.

**The structure shrinks when I use a slicer.** The slicer is filtering the data. Use the disconnected
table with the **Show** field (section 6).

**Nothing disappears when I pick in the slicer.** Check that the measure is in the **Show** field and
that the slicer uses the disconnected table.

**The legend says "N without Aisle, Rack or Level (ignored)".** Those rows arrived empty in one of the three
fields, so they are not drawn. A common cause: the column was typed as **number** in Power Query, so text
values such as `A` or `BLOCO-A` became empty. Set Aisle, Rack and Level to **Text**.

**The camera is too far or too close.** Click **Reframe**.

**The warehouse label has no percentage.** Check that **Occupancy** (with at least one value) and
**Capacity** are in the visual, and that the capacity is not zero.

**There is an Export button?** Not for now. A view export (PDF) was built but is turned off, because the
Power BI administrator can block downloads from visuals ("Download unavailable"). See the project spec,
section 16.

**The gradient does not appear.** The field in **Color by** must arrive as a number.

**The floor plan is mirrored or rotated.** Multiply X (or Y) by -1 in the data. Also check that the X
and Y of a warehouse are not swapped.

**The racks became one block with no corridor.** On the floor plan, corridors are gaps in the
coordinates: leave empty rows between pairs of aisles.

**Walls or zones do not appear.** They only exist on the floor plan (X coord and Y coord). Walls also
need the **Warehouse** field.

**An aisle is pairing with the wrong neighbor (automatic grid).** Use **Aisle pairing → By aisle
number**, and **Pairs start at an even aisle** if the physical pair is 2-3, 4-5.

**It is slow when rotating.** See **Performance**: Light resolution and no beams.

**A new value showed up in gray.** It went over 30 values in categorical mode. Filter, group or use
ranges.
