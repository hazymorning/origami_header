# ◪ Origami Header

<div>
<a href="https://github.com/hazymorning/origami_header/releases/latest"><img alt="Latest release" src="https://img.shields.io/badge/Release-V0.4.3-F6F4F1?labelColor=181715"></a>
<a href="https://github.com/hazymorning?tab=repositories&q=origami"><img alt="Origami suite" src="https://img.shields.io/badge/Origami-Suite-F6F4F1?labelColor=181715&logo=data:image/svg%2Bxml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI%2BPHJlY3QgeD0iMyIgeT0iMyIgd2lkdGg9IjE4IiBoZWlnaHQ9IjE4IiBmaWxsPSJub25lIiBzdHJva2U9IiNGNkY0RjEiIHN0cm9rZS13aWR0aD0iMiIvPjxwb2x5Z29uIHBvaW50cz0iMjEsMyAyMSwyMSAzLDIxIiBmaWxsPSIjRjZGNEYxIi8%2BPC9zdmc%2B"></a>
</div>

<br>

<img alt="Origami Header with two custom headers and the fold-out menu, in light and dark mode" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/hero.webp">

Origami Header hides the dashboard header in Home Assistant, without it ever flashing up. If wanted, a custom header with buttons and cards takes its place, or a menu folds out at a tap on a card.

> [!NOTE]
> Home Assistant has no official way to hide the header, so every solution has to work around it. Kiosk mode and CSS hide it only once it's already on screen, which is why it still flashes up for a moment. Origami Header hides it before the browser shows the dashboard, so it never appears. Without an official way, this relies on how Home Assistant builds the page, and that's the only reason it's still a bit experimental.

[Get started](#get-started) · [Modes](#modes) · [More](#more)

## Get started

<details>
<summary><b>Install the module</b></summary>

<br>

Origami Header needs Home Assistant 2026.9 or newer.

1. Open Origami Header in HACS. If HACS asks to add the repository, select **Add**. Then select **Download**.

   [![Open this repository in HACS.](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=hazymorning&repository=origami_header&category=plugin)

2. Add it to [`configuration.yaml`](https://www.home-assistant.io/docs/configuration/#editing-configurationyaml) as an extra module. If `frontend:` is already there, only the `extra_module_url` lines go under it.

   ```yaml
   frontend:
     extra_module_url:
       - /hacsfiles/origami_header/origami-header.js
   ```

3. Restart Home Assistant.

It has to be an extra module, because a dashboard resource loads too late, when the default header is already on screen.

Without HACS, copy `origami-header.js` from the [latest release](https://github.com/hazymorning/origami_header/releases/latest) to `/config/www` and use `/local/origami-header.js?v=0.4.3` as the extra module. After each update, change the version at the end to the new one, otherwise the browser keeps using the old file.

</details>

<details>
<summary><b>Add the card</b></summary>

<br>

1. Edit a dashboard and add the **Origami Header** card to any view.
2. Choose **No header**, **Custom header** or **Fold-out menu**.

The card changes the header of the whole dashboard and stays invisible itself.

- No header hides the header for everyone. A custom header or the menu shows only for admins, and other users get no header at all, unless `all_users` is on.
- In edit mode, the default header comes back. Edit mode opens from a button with `special: edit`, a card with the `origami_header: edit` action, or with `?edit=1` at the end of the address.

</details>

## Modes

<sub>The images show the [Origami](https://github.com/hazymorning/origami) theme.</sub>

### No header

<img width="800" alt="The top of the Origami dashboard on a phone, with no header above the first card, in light and dark mode" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/hidden.webp">

The header is gone, and the dashboard starts right at the top. This is what Origami Header was made for.

### Custom header

<img width="800" alt="A custom header with the title Home, the temperature and humidity, and buttons for edit mode, automations and the sidebar, in light and dark mode" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/header.webp">

Cards and buttons in the header's place. When the buttons don't fit, they move to a second row.

<details>
<summary><b>The header in the image</b></summary>

<br>

Buttons work like the button badges of a heading card and can have an icon, a name or both. `cards` takes any card, such as a clock. The `css` uses variables of the [Origami theme](https://github.com/hazymorning/origami).

```yaml
type: custom:origami-header-card
mode: header
cards:
  - type: heading
    heading: Home
    badges:
      - type: entity
        entity: sensor.living_room_temperature
        icon: mdi:home-thermometer-outline
      - type: entity
        entity: sensor.living_room_humidity
        icon: mdi:water-percent
buttons:
  - icon: mdi:pencil-outline
    special: edit
  - icon: mdi:robot-outline
    tap_action:
      action: navigate
      navigation_path: /config/automation/dashboard
  - icon: mdi:menu
    special: sidebar
css: |
  :host([mode="header"]) .bar {
    padding: var(--ha-space-3) var(--view-bleed) var(--ha-space-4);
    border-bottom: none;
  }
  :host([mode="header"]) .cards {
    flex: 1 1 auto;
    margin-inline-start: calc(-1 * var(--ha-space-1));
  }
  :host([mode="header"]) .buttons {
    gap: var(--ha-space-2);
  }
  :host([mode="header"]) .button {
    height: var(--control-height-icon);
    min-width: var(--control-height-icon);
    border-radius: var(--radius-inner);
    background: var(--fill-subtle);
    box-shadow: none;
  }
  :host([mode="header"]) .button ha-icon {
    color: var(--color, var(--primary-text-color));
    --mdc-icon-size: var(--icon-size-s);
  }
  :host([mode="header"]) .name {
    font-weight: var(--ha-font-weight-bold);
  }
```

</details>

### Fold-out menu

<img width="800" alt="The fold-out menu of the Origami dashboard, with the server's status and buttons for files, automations, the sidebar and edit mode, in light and dark mode" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/menu.webp">

No header, and a menu that folds out at a tap on another card. The image shows the [menu of the Origami dashboard](https://github.com/hazymorning/origami/tree/main/dashboard/menu).

<details>
<summary><b>Opening the menu</b></summary>

<br>

With `mode: menu`, another card opens the menu with this action:

```yaml
tap_action:
  action: fire-dom-event
  origami_header: toggle
```

`open` and `close` work the same way. In every mode, `edit` turns on edit mode and `sidebar` opens or closes the sidebar. The menu closes at a tap outside or on one of its buttons, with Escape, when it's swiped back, or when another page opens.

</details>

## More

<details>
<summary><b>Options</b></summary>

<br>

The visual editor covers every option, and in edit mode the card shows a preview.

| Option | Default | Description |
| --- | --- | --- |
| `mode` | `header` | `header`, `menu` or `hidden` |
| `buttons` | Edit, Automations, Tools and Sidebar | The buttons, or `[]` for none |
| `cards` | | One card or a list of cards |
| `position` | `top` | The edge the menu opens from, `top` or `bottom` |
| `layout` | `list` | The buttons in the menu, `list` with two columns or `grid` with four |
| `all_users` | `false` | Shows it to everyone. Otherwise other users get no header at all. |
| `css` | | Custom styles, see [Styling](#styling) |

Each button takes `name`, `icon`, `color` (a theme color like `amber` or any CSS color) and `tap_action`, `hold_action` and `double_tap_action`. Instead of a tap action, `special: edit` turns on edit mode and `special: sidebar` opens or closes the sidebar. Only admins see the edit button.

Only the first Origami Header card in a dashboard counts, and it applies to every view. Visibility conditions don't change that.

</details>

<a name="styling"></a>
<details>
<summary><b>Styling</b></summary>

<br>

The buttons follow the theme and use the same variables as cards and badges. For anything else, the `css` option targets these classes:

| Class | Part |
| --- | --- |
| `.bar` | The custom header |
| `.scrim`, `.sheet` | The backdrop and the menu |
| `.cards`, `.buttons` | The cards and the buttons |
| `.button`, `.button ha-icon`, `.button ha-ripple`, `.name` | A button, its icon, its ripple and its name |
| `.note` | The "No header" text in the card preview |

`:host([open])` matches while the menu is open. This blurs the dashboard behind the menu:

```yaml
css: |
  .scrim {
    -webkit-backdrop-filter: blur(12px);
    backdrop-filter: blur(12px);
  }
```

</details>

<details>
<summary><b>How it works</b></summary>

<br>

Home Assistant has a placeholder for the header, and the default header only shows up when nothing else fills it. Origami Header fills it, or hides the header, after the dashboard is built but before the browser shows it, so the default header never appears. If an update changes that placeholder, a custom header or the menu gives way to the default header with a warning, so the dashboard stays usable.

</details>

<sub>This card exists out of pure frustration. For years, a flickering header annoyed me every single day. I searched for a fix, posted in several forums, and never found an answer. Eventually, some intensive research with the help of AI produced a prototype that, while not the cleanest solution technically, turned out to be by far the most effective one. Its main purpose is to hide that header, and everything else it does is a bonus.</sub>
