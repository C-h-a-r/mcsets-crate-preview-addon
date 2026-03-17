# Crate Preview Builder

A self-contained Twig component that renders Minecraft-style inventory grids anywhere on your page. Define crates once, then drop `@crate-preview-builder:<name>` anywhere in your content and the JS will replace it with the full rendered inventory UI.

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
| `empty_slot` | `string\|null` | Item ID to show in blank border cells. `null` for plain empty cells |
| `color_bg_panel` | `string\|null` | Background colour of the outer panel |
| `color_bg_cell` | `string\|null` | Background colour of each slot cell |
| `color_bg_cell_hover` | `string\|null` | Cell background colour on hover |
| `color_bg_tooltip` | `string\|null` | Tooltip background colour |
| `color_text_tooltip` | `string\|null` | Tooltip item name text colour |
| `color_border_cell` | `string\|null` | Overrides all four cell border sides with this colour |
| `color_border_tooltip` | `string\|null` | Tooltip border and arrow colour |
| `color_text_count` | `string\|null` | Stack count number colour |

Any option set to `null` falls back to the active theme's default value.

---

## Item Format

Items are defined as a multiline string in the `items` key of each crate. Each line is one item. Attributes are written as `@attribute="value"` with no spaces between them.

### Attributes

| Attribute | Required | Description |
|---|---|---|
| `@name="..."` | Yes | Display name shown in the tooltip |
| `@item="..."` | No | Item ID for the image API (uses underscores, e.g. `diamond_sword`) |
| `@img="..."` | No | Custom image URL — overrides `@item` if both are set |
| `@quantity="<int>"` | No | Stack count shown in the bottom-right corner of the slot |
| `@lore="..."` | No | Italic lore text shown in the tooltip below the name |
| `@description="..."` | No | Description shown below the lore, separated by a divider |
| `@enchanted` | No | Flag — no value needed. Appends `?glint=true` to the image URL |

Use `\n` inside any value to insert line breaks (works in `@lore` and `@description`).

### Example

```
@name="Diamond Sword"@item="diamond_sword"@quantity="1"@lore="Sharpness V\nUnbreaking III"@description="A finely crafted blade."@enchanted
@name="Apple"@item="apple"@quantity="5"@lore="Restores 4 hunger"
@name="Custom Item"@img="https://example.com/my-item.png"@quantity="1"@lore="One of a kind"
```

---

## Themes

Two built-in themes are available.

### `minecraft`

The classic Minecraft inventory aesthetic. Stone-grey cells with directional lighting borders, the VT323 pixel font, and dark purple tooltips.

### `sleek`

A modern dark UI. Slate-blue panel, rounded cells, Inter font, and clean dark tooltips with a subtle drop shadow.

---

## Colour Overrides

Global colour overrides let you customise the look of a specific crate without creating a new theme. Set any override to a CSS colour value to use it, or `null` to fall back to the theme default.

```twig
legendary: {
    theme:                'sleek',
    color_bg_panel:       '#1a0a2e',
    color_bg_cell:        '#0d0520',
    color_bg_cell_hover:  '#2d1060',
    color_bg_tooltip:     '#1a0a2e',
    color_text_tooltip:   '#e8d5ff',
    color_border_cell:    '#6d28d9',
    color_border_tooltip: '#6d28d9',
    color_text_count:     '#c4b5fd',
    ...
}
```

Note: `color_border_cell` sets all four borders of every cell to the same colour and thickness (`1px solid`). If you need asymmetric borders (like the classic Minecraft inset look), use the `minecraft` theme instead of overriding.

---

## Empty Slot

The `empty_slot` option fills all border cells (the padding row around the item grid) and any unfilled slots in a partial last row with a background item image.

```twig
empty_slot: 'dirt',     // shows a faded dirt block in all blank cells
empty_slot: null,       // blank cells have no image, just the cell background
```

The value should be a valid item ID (same format as `@item="..."`).

---

## Image API

Item images are fetched from:

```
https://api.minecraftitems.xyz/api/item/<item_id>
```

Item IDs use underscores, matching standard Minecraft namespaced IDs — the same names you'd use in `/give` commands:

```
diamond_sword
iron_ingot
enchanted_book
cooked_beef
ender_pearl
```

### Enchantment Glint

Adding `@enchanted` to an item appends `?glint=true` to the image URL:

```
https://api.minecraftitems.xyz/api/item/diamond_sword?glint=true
```

### Custom Images

If you provide `@img="<url>"`, that URL is used directly instead of the API. The `?glint=true` param is still appended if `@enchanted` is also set.

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

3. Use it anywhere on the page:

```
@crate-preview-builder:nether
```

---

## Layout Structure

The grid follows this layout pattern regardless of how many items there are:

```
[ ][ ][ ][ ][ ][ ][ ][ ][ ]   ← blank border row
[ ][I][I][I][I][I][I][I][ ]   ← item row (7 items per row)
[ ][I][I][I][I][I][I][I][ ]   ← item row continues
[ ][ ][ ][ ][ ][ ][ ][ ][ ]   ← blank border row
```

Items fill left to right, top to bottom. If the last row is not full, remaining slots are filled with the `empty_slot` item (or left blank if `empty_slot` is `null`). The number of rows grows automatically based on item count — 7 items per row by default.

---

## Tooltips

Hovering an item reveals a tooltip with up to three layers:

- **Name** — always shown, styled in the theme's tooltip text colour
- **Lore** — italic, shown in a secondary accent colour (purple by default)
- **Description** — muted, shown below a horizontal divider

```
@lore="Sharpness V\nUnbreaking III"
@description="A finely crafted blade."
```

Both `@lore` and `@description` support `\n` for line breaks.
