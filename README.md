<img alt="Origami Header on three phones: a custom header, the fold-out menu in dark mode, and a header with a clock card" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/hero.webp">

Origami Header replaces the dashboard header in Home Assistant with your own buttons and cards. You can turn them into a new header, or into a menu that folds out when you tap a card on your dashboard. You can also hide the header completely.

> [!NOTE]
> This card was created because there still isn't a proper way to actually hide the header in Home Assistant. With kiosk mode or CSS it shows up for a moment anyway, every single time a dashboard opens, and that looks bad. Origami Header solves a problem that has been around for years, but it can be considered a bit hacky and experimental.

[Installation](#installation) · [Usage](#usage) · [Buttons and cards](#buttons-and-cards) · [Opening the menu](#opening-the-menu) · [Options](#options) · [Styling](#styling) · [How it works](#how-it-works)

## Installation

Origami Header needs Home Assistant 2026.9 or newer.

1. Open Origami Header in HACS with the button below. It is not listed in the HACS store, so HACS may ask you to add the repository first. Then click **Download**.

   [![Open your Home Assistant instance and open this repository inside HACS.](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=hazymorning&repository=origami_header&category=plugin)

2. Add these lines to `configuration.yaml`. If `frontend:` or `extra_module_url:` is already in the file, add only the lines that are missing.

   ```yaml
   frontend:
     extra_module_url:
       - /hacsfiles/origami_header/origami-header.js
   ```

3. Restart Home Assistant and reload your browser.

Origami Header has to be loaded as an extra module, not as a dashboard resource. A resource loads too late, and by then the normal header is already on screen. If HACS also adds it as a dashboard resource, you can leave that in place, it won't run twice.

<details>
<summary><b>Manual</b></summary>

<br>

1. Download `origami-header.js` from the [latest release](https://github.com/hazymorning/origami_header/releases/latest).
2. Copy it into `config/www/`.
3. Add it to `configuration.yaml` in the same way as above, but with this path:

   ```yaml
   frontend:
     extra_module_url:
       - /local/origami-header.js?v=0.4.3
   ```

4. Restart Home Assistant and reload your browser.

Change the version in `?v=` after each update, otherwise your browser keeps using the old file.

</details>

## Usage

Add the Origami Header card to any view. It changes the header for the whole dashboard, not just that view. The card itself stays invisible. In edit mode you get the normal header back, and the card shows a preview of your header or menu.

By default only admins see your header or menu, and other users get no header at all. Turn on `all_users` to show it to everyone.

There are three modes, and you pick one at the top of the card editor.

<img alt="The three modes on three phones: a custom header, the open fold-out menu and a dashboard without a header" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/modes.webp">

### Custom header

`mode: header` is the default. Your cards fill the header and the buttons sit on the right. When there isn't enough room, the buttons move to a second row.

<img width="800" alt="Three custom headers: icon buttons, buttons with names, and a clock card" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/header.webp">

### Fold-out menu

`mode: menu` hides the header. Your buttons and cards fold out from the top of the screen when another card [opens the menu](#opening-the-menu). With `position: bottom` they come from the bottom instead.

<img width="800" alt="A card that opens the menu, and the open menu in list and grid layout" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/menu.webp">

<details>
<summary><b>Menu at the bottom</b></summary>

<br>

<img width="600" alt="The fold-out menu opening from the bottom, in light and dark mode" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/bottom.webp">

</details>

### No header

`mode: hidden` hides the header for everyone and puts nothing in its place, which is handy on a wall tablet.

> [!TIP]
> The normal header goes away in every mode, and so does its edit button. To get into edit mode, keep a button with `special: edit` or add `?edit=1` to the dashboard address. Any card can also turn on edit mode with the `edit` command from [Opening the menu](#opening-the-menu).

## Buttons and cards

Buttons work like the button badges of a heading card. In the header they look like badges, and in the menu they look like small cards. Under `cards` you can add any card, for example a heading card with a title and a few entity badges.

When you add Origami Header from the card picker, it starts with buttons for edit mode, automations, tools and the sidebar.

<details>
<summary><b>Button options</b></summary>

<br>

| Option | Description |
| --- | --- |
| `name`, `icon` | The text and the icon on the button. If you leave one out, the button shows only the other. |
| `color` | The color of the icon, either a theme color like `amber` or any CSS color. |
| `special` | `edit` turns on edit mode, and `sidebar` toggles the sidebar like the menu button of the normal header. Use it instead of `tap_action`. Only admins see the edit button. |
| `tap_action`, `hold_action`, `double_tap_action` | What happens when you tap, hold or double tap the button, for example opening another page or turning on a light. |

A button without `tap_action` or `special` does nothing when you tap it, apart from closing the menu.

</details>

<details>
<summary><b>Example</b></summary>

<br>

This is the header with icon buttons from the screenshots:

```yaml
type: custom:origami-header-card
mode: header
cards:
  - type: heading
    heading: Home
    badges:
      - type: entity
        entity: sensor.outside_temperature
      - type: entity
        entity: sensor.system_monitor_processor_use
        icon: mdi:gauge
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

The [examples folder](https://github.com/hazymorning/origami_header/tree/main/examples) has a complete header and a complete menu.

</details>

## Opening the menu

The menu opens from another card, for example a button card or a tile. Give that card a `fire-dom-event` action:

```yaml
tap_action:
  action: fire-dom-event
  origami_header: toggle
```

To close the menu, tap outside of it, press Escape or swipe it back toward the edge it came from. It also closes when you tap one of its buttons or go to another page.

<details>
<summary><b>All commands</b></summary>

<br>

| Command | What it does |
| --- | --- |
| `open` | Opens the menu. |
| `toggle` | Opens the menu, or closes it when it is already open. |
| `close` | Closes the menu. |
| `edit` | Turns on edit mode. |
| `sidebar` | Toggles the sidebar. |

`open`, `toggle` and `close` only work in menu mode.

</details>

## Options

You can set all options in the visual editor or in YAML.

<img width="800" alt="The card's preview in edit mode, and the visual editor with the three modes" src="https://raw.githubusercontent.com/hazymorning/origami_header/main/images/editing.webp">

<details>
<summary><b>Card options</b></summary>

<br>

| Option | Default | Description |
| --- | --- | --- |
| `mode` | `header` | `header` for a custom header, `menu` for a fold-out menu or `hidden` for no header. |
| `buttons` | Edit, Automations, Tools and Sidebar | The buttons in the header or menu. Use `[]` for no buttons. |
| `cards` | | Any cards you want in the header or menu, as a single card or a list. |
| `position` | `top` | Where the menu opens from, `top` or `bottom`. |
| `layout` | `list` | How the buttons are arranged in the menu. `list` has two columns, and `grid` has four with the icon above the name. |
| `all_users` | `false` | Shows the header or menu to everyone. Otherwise only admins see it, and other users get no header. |
| `css` | | Your own CSS for the header or menu, see [Styling](#styling). |

</details>

## Styling

The buttons follow your theme and use the same variables as cards and badges, like `--ha-card-background` and `--ha-badge-size`. For anything else there's the `css` option.

<details>
<summary><b>Classes</b></summary>

<br>

| Class | Description |
| --- | --- |
| `.bar` | The custom header. |
| `.scrim` | The backdrop behind the menu. |
| `.sheet` | The menu. |
| `.cards` | The area with the cards. |
| `.buttons` | The area with the buttons. |
| `.button` | A button. |
| `.button ha-icon` | The icon of a button. |
| `.name` | The name of a button. |
| `.note` | The "No header" text in the card preview. |

For example, this blurs the dashboard behind the open menu instead of darkening it:

```yaml
css: |
  .scrim {
    -webkit-backdrop-filter: blur(12px);
    backdrop-filter: blur(12px);
  }
```

</details>

<details>
<summary><b>Theme variables</b></summary>

<br>

The menu uses these variables from your theme:

| Variable | Description |
| --- | --- |
| `--app-header-background-color` | The background of the menu. A menu at the bottom uses `--primary-background-color` instead. |
| `--ha-box-shadow-m` | The shadow under the menu when it opens from the top. |
| `--ha-dialog-scrim-backdrop-filter` | The filter on the dashboard behind the open menu. |
| `--ha-bottom-sheet-border-radius` | The corner radius of the menu. If it is not set, the menu uses the corner radius of dialogs. |
| `--ha-animation-duration-normal` | How long the menu takes to slide in and out. |

</details>

## How it works

<details>
<summary><b>How the header is replaced</b></summary>

<br>

The dashboard in Home Assistant has a placeholder for the header, and the normal header only shows up when nothing else fills it. Origami Header steps in after Home Assistant has built the dashboard but before the browser shows it. It fills the placeholder with your header or hides the header, so the normal one never appears.

If a Home Assistant update changes that placeholder, Origami Header can't place its own header anymore. It then keeps the normal header and logs a warning in the browser console, so the dashboard stays usable.

</details>

<details>
<summary><b>Which card is used</b></summary>

<br>

Origami Header looks for its card in the dashboard config, not on the screen. It goes through the views in order and uses the first one it finds, even inside a stack, a grid or a conditional card. Visibility conditions, on the card itself or around it, have no effect.

</details>

<br>

> [!NOTE]
> AI is used as a tool in this project.
