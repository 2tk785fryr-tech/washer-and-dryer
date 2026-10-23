# Samsung Washer & Dryer Card for Home Assistant

Dashboard cards for Samsung washers and dryers connected through **[LocalThings](https://github.com/mbillow/localthings)**. Each card draws a front-loading machine. It shows the time left on the machine's display, a progress ring round the door and laundry turning in the drum. Its buttons pause, resume or stop a cycle, or start one with your choice of settings, now or set to finish by a time you pick.

> **Credit:** this card is based on the [hOn Washer & Dryer Card](https://mymediabuild.uk/home-assistant-washer-dryer-card/) by **[My Media Build](https://mymediabuild.uk)**. The design, illustrations and animations are theirs; that card was made for Haier, Candy and Hoover machines, and this version reads Samsung's LocalThings entities instead. If you like it, [buy them a coffee](https://buymeacoffee.com/bobgreggr) ☕.

![Preview](images/preview.png)

## The cards

| Card | What it is |
|---|---|
| **Samsung laundry** (`custom:samsung-laundry-card`) | One machine: the picture, its status, Pause/Resume/Stop, quick toggles, the last cycle and weekly stats |
| **Samsung cycles** (`custom:samsung-cycles-card`) | The machine's cycles as buttons. Pick one, adjust its settings, then start now or set a finish time |
| **Samsung laundry pair** (`custom:samsung-laundry-pair-card`) | Optional: washer and dryer side by side in one card, with a status line for both. Stays side by side on a phone, with a compact layout |

## What it shows

- **Time left** on the machine's display, counting down every second, with the percentage done underneath. Before a cycle starts it shows the selected cycle's length and "Ready".
- **A progress ring round the door**: blue for the washer, orange for the dryer, light blue while the dryer cools down or runs wrinkle prevent, and green when the cycle is finished.
- **The current phase** on the door glass: Weight sensing, Washing, Rinsing, Spinning, Drying, Cooling. The drum's speed follows it: slow while weight sensing, steady while washing, a fast blur during spin (with the water draining), and gentler while cooling.
- **Cycle effects:** only white laundry on a Whites cycle. On a steam cycle (Steam Sanitize+, Steam Refresh, Wrinkle Away, or a steaming phase) the glass fogs up and wisps rise from the top of the door. Steam only shows while a cycle is running. Washer Self-Clean shows water and no laundry.
- **Your machine's colour:** White (classic, the default), Pearl, Onyx, Graphite, Midnight blue, or any hex colour.
- **Cycle, temperature and spin** (washer) or **dry level** (dryer), and the finish time.
- **Pause, Resume and Stop buttons** while a cycle runs. Stop asks first. Pause and Resume need Smart Control on; Stop works without it.
- **Quick toggles** under the machine: Wrinkle prevent and Damp alert (dryer), and the end-of-cycle Sound (both).
- **Last cycle:** for example "Steam Sanitize+ · 1 h 30 min · 2.8 kWh · ended 2:23 PM". Water is shown too on machines that report it.
- **Weekly stats:** for example "3 loads this week · mostly Heavy Duty · 12.3 kWh this month".
- **Drum-clean reminder:** when the washer says a drum clean is due, a banner with a **Start Self-Clean** button.
- **A Smart Control icon**, and an error message if the machine reports an alarm code.

### With an optional door sensor

Samsung machines don't report their door. If you add a contact sensor to the door, the card also shows:

- **The door opening:** the door in the picture swings open on its hinge, and the door icon shows open, closed, or locked while a cycle runs.
- **Clothes left inside:** after any cycle ends with the door shut, the glass says **Wet clothes in the washer** or **Dry clothes in the dryer**, the label at the top says **Ready to unload**, and the laundry sits still at the bottom of the drum until the door is opened. Washer Self-Clean leaves the drum empty instead. This comes from Home Assistant's recorded history, so it survives restarts. An optional input_boolean that's on while laundry is waiting backs it up when there's no history.
- **"Open door to dry"** on the washer when its door is shut while it's idle and empty, so the drum can dry.
- **No starting with the door open:** the cycles card won't start a cycle until the door is closed.

Without a door sensor, everything else works the same.

### Starting a cycle

Tap a cycle on the cycles card. It's selected on the machine, and you get dropdowns for the settings that cycle allows: temperature, spin, soil level and rinses on the washer, dry level and dry time on the dryer. Under **When**, choose **Start now** or **Finish by** and pick a time; the machine works out the delay. Machines without a finish time get **Start later** with a delay in hours instead.

## Requirements

- The **LocalThings** integration (HACS), with your washer and/or dryer added.
- **Smart Control** switched on at the machine to start, pause or resume it from Home Assistant. Samsung only accepts these commands while it is on: turn the machine on, load it, and press Smart Control. Without it the card still shows everything, and Stop still works.

The card only uses LocalThings entities. Copies of the same machines in the core SmartThings integration are ignored, because SmartThings doesn't expose the cycle picker, progress or buttons for them.

## Install

### HACS

1. In HACS, open the three-dot menu and choose **Custom repositories**.
2. Paste this repository's URL and choose the type **Dashboard**.
3. Search for **Samsung Washer & Dryer Card**, download it, and reload your browser.

### Manual

1. Copy `samsung-laundry-card.js` to `config/www/samsung-laundry-card.js`.
2. Go to **Settings → Dashboards → ⋮ → Resources**, add `/local/samsung-laundry-card.js?v=1.6.0` as a **JavaScript module**, and reload your browser.

## Set up

The easiest way is the dashboard editor:

1. **Edit dashboard → Add card**, and search for **Samsung laundry**, **Samsung cycles** or **Samsung laundry pair**.
2. Choose **Washer** or **Dryer**, and pick your machine under **Machine**. That's the LocalThings device; the card finds every entity it needs on it, even renamed ones. With only one washer and one dryer you can leave it empty and the card finds them itself.
3. Optional: pick a **Door sensor** and a **Laundry-waiting helper**, and under **Look** and **Show on the card** choose the colour and which parts to show.

Or in YAML, washer on the left and dryer on the right, each above its cycle buttons:

```yaml
type: grid
columns: 2
square: false
cards:
  - type: vertical-stack
    cards:
      - type: custom:samsung-laundry-card
        appliance: washer
        body_colour: midnight_blue
      - type: custom:samsung-cycles-card
        appliance: washer
        favourites: ["01", "53", "65", "7c", "57", "04", "7d", "5e"]
  - type: vertical-stack
    cards:
      - type: custom:samsung-laundry-card
        appliance: dryer
        body_colour: midnight_blue
      - type: custom:samsung-cycles-card
        appliance: dryer
        favourites: ["01", "2f", "32", "07", "05", "06", "36", "33"]
```

Or both machines in one card:

```yaml
type: custom:samsung-laundry-pair-card
body_colour: midnight_blue
cycles: true
```

### Options

| Option | Card | What it does |
|---|---|---|
| `appliance` | machine, cycles | `washer` or `dryer`. Default `washer` |
| `device` | machine, cycles | The LocalThings device (the editor's **Machine** picker). Found automatically if left out |
| `entities` | machine, cycles | Override any entity the card found, e.g. `{ progress: sensor.my_washer_progress }`. The names are the `ROLES` at the top of the card file |
| `entity` | machine, cycles | Older alternative to `device`: the start of the machine's entity IDs, e.g. `laundry_room_samsung_washer` |
| `name` | machine, cycles | Card title. Default Washer / Dryer |
| `colour` | machine, cycles | Accent colour, e.g. `"#4fb3ff"` |
| `door` | machine, cycles | Optional door sensor, `on` = open. Found automatically when it's named `<machine>_door_door` or `<machine>_door`. `false` turns it off |
| `unload_helper` | machine | Optional input_boolean that's on while laundry is waiting, used when there's no recorded history. Default `input_boolean.<washer or dryer>_unacknowledged` if it exists; `false` turns it off |
| `cycle_names` | machine, cycles | Names for cycle codes that would otherwise show as "Cycle 47", e.g. `{ "47": "Towels" }` |
| `body_colour` | machine, pair | `white` (classic, default), `pearl`, `onyx`, `graphite`, `midnight_blue`, or any hex colour, e.g. `"#1d2840"` |
| `style` | machine, pair | `photo` (default, a detailed front-loader illustration) or `drawn` (a simple drawing) |
| `max_width` | machine, pair | How wide the machine can get, e.g. `380px` |
| `images` | machine, pair | Folder with your own `washer.svg`, `dryer.svg` and `laundry-drum.webp`, e.g. `/local/myimg/` |
| `door_hint` | machine | "Open door to dry". Default `true` for the washer, `false` for the dryer |
| `toggles` | machine, pair | Quick toggles. Default `true` |
| `summary` | machine, pair | Last cycle. Default `true` |
| `stats` | machine, pair | Weekly stats. Default `true` |
| `favourites` | cycles | Cycle codes to show as buttons; the rest go behind **All cycles**. Default: every cycle the machine offers |
| `title` | pair | Card title. Default Laundry |
| `washer_device`, `dryer_device` | pair | The two machines. Found automatically if left out |
| `washer_door`, `dryer_door` | pair | Their optional door sensors |
| `cycles` | pair | Show each machine's cycles card underneath it. Default `false` |
| `washer`, `dryer` | pair | Options for one machine only, e.g. `washer: { name: Washing machine }` |

### Cycle codes

Samsung identifies cycles by short hex codes. LocalThings translates the ones it knows, and the card uses the same names you see in Home Assistant. On these machines:

| Washer | | Dryer | |
|---|---|---|---|
| `01` | Normal | `01` | Normal |
| `53` | Heavy Duty | `2f` | Heavy Duty |
| `88` | Pet Care | `32` | Perm Press |
| `7c` | Whites | `33` | Steam Sanitize+ |
| `65` | Colors | `05` | Bedding |
| `7d` | Bedding/Waterproof | `36` | Air Fluff |
| `17` | Downloaded | `06` | Time Dry |
| `7e` | Self-Clean | `07` | Delicates |
| `5e` | Rinse+Spin | `42`, `43`, `44`, `47` | not in LocalThings' list yet |
| `86` | Deep Wash | | |
| `04` | Quick Wash | | |
| `57` | Delicates | | |

To name an unknown code, pick that cycle on the machine, look at **select.…_cycle** in **Developer tools → States**, and add it to `cycle_names` on both cards:

```yaml
cycle_names:
  "47": Towels
```

Cycles downloaded through the SmartThings app (such as Wool or Perm Press on the washer) already show by name.

## Releasing an update

Raise `SAMSUNG_CARD_VERSION` at the top of `samsung-laundry-card.js` and push to `main`. The **Release** workflow (`.github/workflows/release.yml`) then publishes a GitHub release tagged with that version, e.g. `v1.6.0`, and HACS offers it as an update. Pushes that don't change the version don't make a release.

## Licence

MIT. Based on the hOn Washer & Dryer Card by [My Media Build](https://mymediabuild.uk) (MIT); their copyright notice is kept in `LICENSE`. Not affiliated with or endorsed by Samsung.
