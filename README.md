<img alt="Origami Header above three phones with custom headers: icon buttons, buttons with names and a clock" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/hero.webp">

Origami Header replaces the dashboard header in Home Assistant with your own buttons and cards. They can form a new header or a menu that folds out when you tap a card, or you can hide the header completely.

> [!NOTE]
> There is still no proper way to hide the header in Home Assistant. With kiosk mode or CSS it shows up for a moment anyway, every time a dashboard opens, and that looks bad. Origami Header solves a problem that has been around for years, but it's a bit hacky and experimental.

## Installation

[![Open your Home Assistant instance and open this repository inside HACS.](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=hazymorning&repository=origami_header&category=plugin)

Download Origami Header in HACS, add it to `configuration.yaml` as an extra module and restart Home Assistant. It has to be an extra module, because a dashboard resource loads too late and the default header would already be on screen. If `frontend:` is already in the file, add only the `extra_module_url` lines. You need Home Assistant 2026.9 or newer.

```yaml
frontend:
  extra_module_url:
    - /hacsfiles/origami_header/origami-header.js
```

<details>
<summary><b>Manual installation</b></summary>

<br>

Download `origami-header.js` from the [latest release](https://github.com/hazymorning/origami_header/releases/latest) into `config/www/` and use `/local/origami-header.js?v=0.4.3` as the extra module instead. Change the version after each update, otherwise your browser keeps using the old file.

</details>

## Usage

Add the Origami Header card to any view and pick a mode. It changes the header of the whole dashboard, and only admins see it unless you turn on `all_users`. The card itself stays invisible. In edit mode the default header comes back, and you get there with a button that has `special: edit`, a card with the `origami_header: edit` action, or by adding `?edit=1` to the address.

<img alt="The three modes on three phones: a custom header, the open fold-out menu and a dashboard without a header" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/modes.webp">

<details>
<summary><b>Custom header</b></summary>

<br>

Your cards fill the header and the buttons sit on the right. When there isn't enough room, the buttons move to a second row. Buttons work like the button badges of a heading card and can have an icon, a name or both, and `cards` takes any card, like a clock.

```yaml
type: custom:origami-header-card
mode: header
cards:
  - type: heading
    heading: Home
    badges:
      - type: entity
        entity: weather.home
        state_content: temperature
buttons:
  - icon: mdi:pencil-outline
    special: edit
  - icon: mdi:robot-outline
    tap_action:
      action: navigate
      navigation_path: /config/automation/dashboard
  - icon: mdi:menu
    special: sidebar
```

</details>

<details>
<summary><b>Fold-out menu</b></summary>

<br>

With `mode: menu` the header is hidden, and the menu folds out when another card fires this action:

```yaml
tap_action:
  action: fire-dom-event
  origami_header: toggle
```

<img alt="The fold-out menu in list and grid layout" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/menu.webp">

The menu closes when you tap outside, press Escape, swipe it back or tap one of its buttons. Besides `toggle`, the action also takes `open`, `close`, `edit` and `sidebar`.

</details>

<details>
<summary><b>Options</b></summary>

<br>

You can set everything in the visual editor, and in edit mode the card shows a preview.

<img alt="The card's preview in edit mode and the visual editor" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/editing.webp">

| Option | Default | Description |
| --- | --- | --- |
| `mode` | `header` | `header`, `menu` or `hidden` |
| `buttons` | Edit, Automations, Tools and Sidebar | The buttons, or `[]` for none |
| `cards` | | One card or a list of cards |
| `position` | `top` | The edge the menu opens from, `top` or `bottom` |
| `layout` | `list` | The buttons in the menu, `list` with two columns or `grid` with four |
| `all_users` | `false` | Shows it to everyone. Otherwise other users get no header at all. |
| `css` | | Your own styles, see [Styling](#styling) |

Each button takes `name`, `icon`, `color` (a theme color like `amber` or any CSS color) and `tap_action`, `hold_action` and `double_tap_action`. Instead of a tap action, `special: edit` turns on edit mode and `special: sidebar` toggles the sidebar. Only admins see the edit button.

Only the first Origami Header card of a dashboard counts, and visibility conditions on it are ignored.

</details>

<a name="styling"></a>
<details>
<summary><b>Styling</b></summary>

<br>

The buttons follow your theme and use the same variables as cards and badges. For anything else, the `css` option targets these classes:

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

Home Assistant has a placeholder for the header, and the default header only shows up when nothing else fills it. Origami Header fills it with your header, or hides the header, after the dashboard is built but before the browser shows it. So the default header never appears. If an update changes that placeholder, Origami Header keeps the default header and logs a warning, so the dashboard stays usable.

</details>

<sub>AI is used as a tool in this project.</sub>
