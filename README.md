# Crate Preview Builder

A self-contained Twig component that renders Minecraft-style inventory grids anywhere on your page. Define crates once, then drop `@crate-preview-builder:<n>` anywhere in your content and the JS will replace it with the full rendered inventory UI.

The component is fully responsive — it fills 100% of its parent container width and scales the grid proportionally, keeping all cells perfectly square at any size.

---

## Installation

Copy `crate-preview.twig` into your templates directory, e.g. `components/crate-preview.twig`.

Include it once in your layout, ideally before `</body>`:

```twig
{% include 'components/crate-preview.twig' %}
```

That's it. The include outputs no visible HTML — it just loads the crate definitions and JS onto the page.

---

## Basic Usage

Once the component is included, write a placeholder anywhere in your page content:

```
@crate-preview-builder:epic
@crate-preview-builder:starter
@crate-preview-builder:legendary
```

The JS scans the DOM on `DOMContentLoaded`, finds these strings in any text node, and replaces them with the rendered inventory grid inline. This works in CMS content fields, plain HTML, Twig templates — anywhere the string ends up as a text node in the DOM.

The rendered grid fills 100% of the width of its containing element. Place it inside any div and it will conform to that width automatically.

---

## Defining Crates

All crates are defined inside `crate-preview.twig` in the `{% set crates = { ... } %}` block at the top of the file. Each key is the crate name used in the placeholder.

```twig
{% set crates = {

    my_crate: {
        theme:                'sleek',
        empty_slot:           null,
        color_bg_panel:       null,
        color_bg_cell:        null,
        color_bg_cell_hover:  null,
        color_bg_tooltip:     null,
        color_text_tooltip:   null,
        color_border_cell:    null,
        color_border_tooltip: null,
        color_text_count:     null,
        items: "
@name=\"Diamond\"@item=\"diamond\"@quantity=\"5\"
        "
    },

} %}
```

### Crate Options

| Option | Type | Description |
|---|---|---|
| `theme` | `string` | `'minecraft'` or `'sleek'` |
| `empty_slot` | `string\|null` | Item ID shown faded in blank border cells. `null` for plain empty cells |
| `color_bg_panel` | `string\|null` | Panel background colour |
| `color_bg_cell` | `string\|null` | Cell background colour |
| `color_bg_cell_hover` | `string\|null` | Cell background on hover |
| `color_bg_tooltip` | `string\|null` | Tooltip background |
| `color_text_tooltip` | `string\|null` | Tooltip item name colour |
| `color_text_count` | `string\|null` | Stack count number colour |
| `color_border_cell` | `string\|null` | Overrides all four cell border sides with this colour |
| `color_border_tooltip` | `string\|null` | Tooltip border and arrow colour |

Any option set to `null` falls back to the active theme's default.

### Per-Crate Shape & Spacing Overrides

These override the corresponding theme value for this crate only:

| Option | Example | Description |
|---|---|---|
| `override_cell_gap` | `"8px"` | Gap between cells |
| `override_cell_radius` | `"12px"` | Cell corner roundness |
| `override_cell_border_width` | `"2px"` | All cell border thickness |
| `override_panel_radius` | `"20px"` | Panel corner roundness |
| `override_panel_padding` | `"24px"` | Panel inner padding |
| `override_img_size_ratio` | `"0.8"` | Image size as a fraction of cell size (0–1) |
| `override_tooltip_radius` | `"10px"` | Tooltip corner roundness |
| `override_tooltip_border_width` | `"2px"` | Tooltip border thickness |
| `override_items_per_row` | `5` | Number of item slots per row |

---

## Item Format

Items are defined as a multiline string in the `items` key of each crate. Each line is one item. Attributes are written as `@attribute="value"` with no spaces between them on the same line.

### Attributes

| Attribute | Required | Description |
|---|---|---|
| `@name="..."` | Yes | Display name shown in the tooltip |
| `@item="..."` | No | Item ID for the image API (underscores, e.g. `diamond_sword`) |
| `@img="..."` | No | Custom image URL — overrides `@item` if both are set |
| `@quantity="<int>"` | No | Stack count shown in the bottom-right corner |
| `@lore="..."` | No | Italic lore text shown below the name in the tooltip |
| `@description="..."` | No | Description shown below lore, separated by a divider |
| `@enchanted` | No | Flag — no value needed. Appends `?glint=true` to the image URL |

Use `\n` inside any value to insert line breaks in `@lore` and `@description`.

### Example

```
@name="Diamond Sword"@item="diamond_sword"@quantity="1"@lore="Sharpness V\nUnbreaking III"@description="A finely crafted blade."@enchanted
@name="Apple"@item="apple"@quantity="5"@lore="Restores 4 hunger"
@name="Custom Item"@img="https://example.com/my-item.png"@quantity="1"@lore="One of a kind"
```

---

## Themes

Two built-in themes are available. Themes define all default visual properties. Per-crate colour and shape overrides are applied on top.

### `minecraft`

Classic Minecraft inventory aesthetic. Stone-grey cells with directional lighting borders (light top-left, dark bottom-right), VT323 pixel font, and dark purple tooltips.

### `sleek`

Modern dark UI. Slate-blue panel, uniform borders, rounded corners, Inter font, and subtle dark tooltips with a drop shadow.

---

## Theme Customisation

Themes are defined in the `{% set themes = { ... } %}` block. Every visual property is exposed as its own key. You can edit theme defaults directly, or create new themes by adding a new key.

### Full Theme Reference

**Panel**

| Key | Description |
|---|---|
| `font_url` | Google Fonts URL to load |
| `font_family` | CSS font-family value |
| `panel_bg` | Background colour |
| `panel_padding` | Inner padding (CSS shorthand) |
| `panel_radius` | Corner roundness |
| `panel_shadow` | Box shadow |
| `panel_border_top` | Full border shorthand for top side |
| `panel_border_left` | Full border shorthand for left side |
| `panel_border_right` | Full border shorthand for right side |
| `panel_border_bottom` | Full border shorthand for bottom side |

**Grid & Cells**

| Key | Description |
|---|---|
| `cell_size` | Reference cell size used to calculate proportional gap |
| `cell_gap` | Reference gap size used to calculate proportional gap |
| `cell_bg` | Cell background |
| `cell_hover_bg` | Cell background on hover |
| `cell_hover_transform` | CSS transform on hover e.g. `scale(1.05)` |
| `cell_hover_shadow` | Box shadow on hover |
| `cell_radius` | Cell corner roundness |
| `cell_border_top` | Full border shorthand — top |
| `cell_border_left` | Full border shorthand — left |
| `cell_border_right` | Full border shorthand — right |
| `cell_border_bottom` | Full border shorthand — bottom |

**Images & Counts**

| Key | Description |
|---|---|
| `img_size_ratio` | Image width/height as a fraction of cell size e.g. `"0.7"` |
| `count_weight` | Stack count font weight |
| `count_color` | Stack count text colour |
| `count_shadow` | Stack count text shadow |

**Tooltips**

| Key | Description |
|---|---|
| `tooltip_bg` | Background colour |
| `tooltip_border_width` | Border thickness e.g. `"1px"` |
| `tooltip_border_style` | Border style e.g. `"solid"` |
| `tooltip_border_color` | Border colour (also used for the arrow) |
| `tooltip_radius` | Corner roundness |
| `tooltip_padding` | Inner padding |
| `tooltip_shadow` | Box shadow |
| `tooltip_text` | Item name text colour |
| `tooltip_lore_color` | Lore text colour |
| `tooltip_desc_color` | Description text colour |
| `tooltip_size` | Font size |
| `tooltip_weight` | Font weight |
| `tooltip_text_shadow` | Text shadow |
| `tooltip_tracking` | Letter spacing |

**Layout**

| Key | Description |
|---|---|
| `items_per_row` | Number of item slots per row (default 7) |

---

## Responsive Layout

The grid uses `flex: 1 1 0` on every cell so they divide the parent width equally, and `aspect-ratio: 1 / 1` to keep cells square. The gap between cells is expressed as a percentage of the row width, derived proportionally from the theme's `cell_size` and `cell_gap` reference values.

This means the grid always fills 100% of its parent container with no JS measurement or transforms required. Resize the browser and it reflows automatically.

---

## Image API

Item images are fetched from:

```
https://api.minecraftitems.xyz/api/item/<item_id>
```

Item IDs use underscores, matching standard Minecraft namespaced IDs:

```
diamond_sword
iron_ingot
enchanted_book
cooked_beef
ender_pearl
netherite_sword
```

### Enchantment Glint

Adding `@enchanted` to an item appends `?glint=true` to the image URL:

```
https://api.minecraftitems.xyz/api/item/diamond_sword?glint=true
```

### Custom Images

If you provide `@img="<url>"`, that URL is used directly instead of the API. `?glint=true` is still appended if `@enchanted` is also present.

---

## Empty Slot

The `empty_slot` option fills all border cells (the padding row around the item grid) and any unfilled slots in a partial last row with a faded background item image.

```twig
empty_slot: 'dirt',   {# shows a faded dirt block in all blank cells #}
empty_slot: null,     {# blank cells are plain colour only #}
```

---

## Adding a New Crate

1. Open `crate-preview.twig`
2. Add a new entry to the `{% set crates = { ... } %}` block:

```twig
nether: {
    theme:                'minecraft',
    empty_slot:           'netherrack',
    color_bg_panel:       null,
    color_bg_cell:        null,
    color_bg_cell_hover:  null,
    color_bg_tooltip:     null,
    color_text_tooltip:   null,
    color_border_cell:    null,
    color_border_tooltip: null,
    color_text_count:     null,
    items: "
@name=\"Blaze Rod\"@item=\"blaze_rod\"@quantity=\"8\"@lore=\"Dropped by Blazes\"
@name=\"Nether Star\"@item=\"nether_star\"@quantity=\"1\"@lore=\"Rare drop from the Wither\"@enchanted
@name=\"Magma Cream\"@item=\"magma_cream\"@quantity=\"16\"
    "
},
```

3. Use it anywhere:

```
@crate-preview-builder:nether
```

---

## Adding a New Theme

Add a new key to the `{% set themes = { ... } %}` block with all required properties, then reference it in a crate with `theme: 'my_theme'`. All keys listed in the Full Theme Reference above are required.

---

## Grid Layout

```
[ ][ ][ ][ ][ ][ ][ ][ ][ ]   ← blank border row
[ ][I][I][I][I][I][I][I][ ]   ← item row (7 items per row by default)
[ ][I][I][I][I][I][I][I][ ]   ← item row continues if more than 7 items
[ ][ ][ ][ ][ ][ ][ ][ ][ ]   ← blank border row
```

Items fill left to right, top to bottom. Unfilled slots in the last row are blank (or show `empty_slot` if set). Rows grow automatically based on item count.

---

## Tooltips

Hovering an item reveals a tooltip with up to three layers:

- **Name** — always shown, in the theme's tooltip text colour
- **Lore** — italic, in the lore colour (purple by default). Supports `\n` for line breaks
- **Description** — muted, below a horizontal divider. Supports `\n` for line breaks

```
@lore="Sharpness V\nUnbreaking III"
@description="A finely crafted blade."
```
