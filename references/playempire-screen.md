# The Playempire play screen

What a player sees at `/games/<name>/play`, and how each part relates to the
Empire commands underneath. Load this whenever a question is about the
website rather than the game: a pane, a button, the map's colors, an empty
box, a message the site printed instead of Empire. Checked against the site
on 2026-11-01; the site itself is authoritative if the two disagree.

The rule underneath every pane: **the site never runs a command that costs
BTUs on the player's behalf**. It fills most panes itself with reads that
cost nothing, refreshed after the player's own command output or an update
lands, never on a timer. A pane that says "unknown" is waiting for the player
to run a command that costs BTUs (the Budget pane and `budget`).

## Layout

- **Sidebar**, top to bottom: game name and country; **Game clock**;
  **Command help**; **First steps** (until finished or dismissed);
  **Messages**; **Nation**; **News and relations**; **Warnings**; **Budget**.
- **Main area**: a **While you were away** box when there's news, the
  **map** with its sector box beside it, and the **transcript** — Empire's
  own output, a command at a time.
- **Footer**: the command box, its **Send**/**Answer** button, the player's
  BTU count, and a status line for refusals.

## Game clock

**Turn** is the game's update count; it shows "—" until the site has read a
turn number from the game. **Next update** counts down to the next update
("updating…" while it runs). **Updates** says how often they come ("every 10
minutes" by default). Nothing is due before the countdown ends: an update
just advances the world (`nation-levels.md` § The update).

## The command box

- Type an Empire command and press **Send** (or Enter). Tab completes a
  command name; the up and down arrows walk back through earlier commands.
- When a command asks a follow-up question (most do, if you leave off
  arguments — `move` asks for a path, `designate` for a type), the question
  appears at the end of the transcript and the button changes to **Answer**:
  the next line answers the question instead of starting a new command.
  Beside Empire's wording the site shows what the question means in plain
  English, and when there's a fixed set of answers (yes/no, a one-letter
  choice) it offers them as buttons that fill the box; the player still
  sends it.
  An empty answer backs out of most questions, changing nothing; a few
  complain as they go (an empty answer to `designate` prints "No such
  designation"). For `move` and `explore` directions, `h` stops where you
  are. Careful with stray letters: some answers are meaningful (`n` to
  `designate` means nuclear plant). Typing the whole command on one line
  (`designate 2,4 h`) skips the questions. What an empty or odd answer does
  to a particular question is in the code that asks it (`SKILL.md` §
  Checking a fact against the server's source).
- The **BTU** count beside the button is the player's BTUs as of the last
  command prompt. Players never see Empire's raw prompt numbers; the gateway
  reads them.
- Refusals from the site itself (not from Empire) land in the status line
  under the box.

## The map

- It shows only what the player's country knows, in three levels: sectors it
  **owns** (full detail), sectors **in sight** now (the type character only;
  another country's land shows `?`), and sectors **remembered** from earlier
  sightings (possibly out of date). Everything else is blank dark space — the
  rest of the world is there, just unseen. Each country starts on its own
  island (`SKILL.md` § The world), so early on the map is mostly empty.
- Before `break` the map can't be read at all; the pane says so.
- It refreshes after each command's output and each update, never on a timer.
- **Buttons above the map**: the first row shades each owned sector by one
  figure — **None** (the default), **Efficiency**, **Mobility**,
  **Civilians**, **Military**, or **Food** (Food appears only in games where
  people eat). A stronger fill means more: efficiency against 100%,
  mobility against its 127 cap, and civilians, military and food against
  your own largest sector. It's a glance aid with no legend; the sector box
  or `census` gives the numbers. The last, **Distribution**, colors nothing:
  it draws an amber arrow from each sector that distributes to another
  (`distribute`) along the route Empire would use to its center, and a red
  cross on a sector that has no route there. The routes are read when the
  button is pressed, so press it again after changing them. The choice is remembered in the browser and
  sends nothing to the game. Then zoom out, zoom in, **Show capital**, and
  open the map full-size. The map scrolls; there are no pan buttons.
- **Small rectangles in a sector's corners** show where the player's own units
  are: top-left for ships, top-right for planes, bottom-left for land units.
  There's one rectangle per kind, however many units are there. It is **red**
  if any of them is a military type and **blue** if all are economic (fishing
  boats, cargo ships, tankers, recon planes and satellites, supply units,
  trains, radar units and the like). It is **solid** when every unit there is
  at 100% efficiency and **hollow** when any is below, which includes a unit
  still being built: new ships start at 20%, new planes and land units at 10%.
  Holding the pointer anywhere on a sector lists the player's units there
  under the sector's own details: ships, then planes, then land units, one
  line per fleet, wing or army (`fleet a: 2 destroyers, cargo ship`,
  `wing a: 2 Sopwith Camels`), then each kind's units in none. A named ship
  appears by name, the rest by type with a count. A plane or land unit
  aboard a ship or another land unit isn't listed on its own; the sector box
  shows it under its carrier. Nukes and other countries' units aren't marked.
- **Clicking a sector** selects it and fills the **sector box** beside the
  map:
  - a sector the player owns: coordinates, designation (a pending change
    shows as an arrow, `m → a`), efficiency, mobility, people, food, any other
    goods, and the ground's resource ratings; where it distributes to and its
    thresholds, if it has any (also in the sector's hover); and a
    **Designate** button that puts `designate x,y ` in the command box (the
    player still sends it);
  - a sector in sight but not owned: its coordinates and "In sight", nothing
    more — Empire gives a country no figures for land it doesn't own (`spy`
    and scouting are how to learn more; `diplomacy-and-communication.md` §
    Scouting);
  - a remembered or never-seen sector: coordinates only;
  - any sector with the player's units, at sea too: under the rest, ships by
    fleet, planes by wing and land units by army ("Fleet a", "No fleet",
    "Wing a", "No wing", ...), each with its number (what unit commands
    take), type and name, efficiency, mobility and tech. A ship adds its speed
    (Spd), firing range (Rng) and how many guns it can fire (Fir), the same
    figures `sstat` prints, which grow when the ship's tech is well above its
    type's, and its cargo against what it can carry (`Food 12/60`). A plane
    adds the range it's set to fly and any nuke it carries; a land unit its
    fortification (Fort), the efficiency it retreats below (Ret, set with
    `morale`) and its cargo. Units aboard show under their carrier. Each also
    says whether it is stopped (no repairs or production until the update),
    on a mission or has retreat orders. A long list scrolls on its own. It's
    as fresh as the Nation pane, which reloads after each command.
- **The sector table**, below the map (scroll the map pane down to reach
  it): one row for each sector the player owns, with its coordinates,
  designation, efficiency, mobility, civilians, military, food and the
  ground's resource ratings. Clicking a heading sorts by that column;
  clicking a coordinate selects that sector on the map. The title above it
  folds it away. It's the same figures `census` prints, laid out to sort;
  `census` also has columns the table doesn't, such as `work` and `avail`.
- While a command is being typed and the next thing it needs is a sector,
  clicking a cell types that sector's coordinates into the command instead of
  selecting it. For `move` and `explore` to a sector you own, it fills in
  Empire's best route there. For an `explore` into a sector you don't own
  yet, it puts in only the coordinates, which Empire can't route, so type the
  path instead.
  With `navigate` and its ships typed (`navigate 12 `, `navigate a `),
  clicking a sector fills in a sea route there that the site works out from
  the player's own map, going around land the map shows and treating unknown
  sectors as open water, as Empire does. If the ships meet land the map
  didn't show, Empire stops them there and asks for the rest of the path.
  The mouse pointer over the map shows which a click will do.
- Coordinates in the transcript are links: clicking one selects that sector
  on the map.
- Typing direction letters as a command's path (`move c 0,0 10 jjn`) draws the
  route on the map as you type, and the command help pane shows its mobility
  cost. For `navigate` the route starts at the flagship, the first of the
  ships that can sail, and a step into land the map shows is marked with a
  red cross.

## Sidebar panes

- **Command help** — shows the manual entry for the command being typed.
  **Open** opens the full manual in a dialog. Typing `info` or `help` at the
  prompt does the same thing; the gateway doesn't pass them to Empire. The
  whole manual is also at <https://playempire.net/manual>.
- **Messages** — where players write to each other. Empire's own `telegram`,
  `announce`, `flash`, `wall` and `wire` are intercepted by the gateway, which
  answers by pointing here. **Open** shows the message log and a form
  whose **To** offers *a country*, *every country*, or *your allies* (the
  countries **you** have declared Allied, read from the game when you send).
  Relations don't gate messages; blocking does: a player can block another,
  which stops messages both ways in every game, and can report a message to
  the site's moderators. Messages are stored by the site and are not
  end-to-end encrypted: the site's admins can read any of them, not only
  reported ones, so players shouldn't put real-world passwords or anything
  personal and secret in one. The pane shows the unread count and a
  **Browser notifications** checkbox; while the tab is hidden its title
  counts new messages.
- **Nation** — the country's figures (money, levels, reserve, capital) and
  tables of its ships, planes, land units and nukes. The ship, plane and
  land unit tables also give each unit's number (what unit commands take)
  and its fleet, wing or army letter, blank for one in none. Clicking a
  heading sorts that table, so sorting by fleet puts a fleet's ships
  together. The site fills it
  itself from free reads, so it needs no command and costs no BTUs; it's
  empty while the country is in sanctuary. `nation` at the prompt prints the
  same figures, also free.
- **News and relations** — the newest eight world headlines, and for each
  other country the relation each side has declared ("you neutral, they
  hostile"). Blank until `break`.
- **First steps** — a checklist for a new country: `break`; scroll the map
  pane down to the sector table; `explore`; `designate`; `move` more
  civilians into a sector you designated; `budget`. Clicking a command name
  puts it in the command box; the player sends it. The sector table step has
  no button: it ticks once the player scrolls the pane down to the table's
  end. The `move` step ticks only for civilians moved into a sector an
  earlier `designate` named, with the destination or path typed on the
  command line. Order is suggested, not enforced, except that nothing else
  works until `break`. **Dismiss** hides it.
- **Warnings** — the latest `starvation` report when it names a sector that
  will starve; "(none)" otherwise.
- **Budget** — the player's most recent `budget` output, laid out as income,
  expenses and the estimated new treasury. It says "(unknown - command
  budget)" until the player runs `budget`, which costs 1 BTU, so the site
  never runs it for them; the word `budget` there is a button that fills the
  command box. Once the budget shown is five or more turns old, a **Reload
  (1 BTU)** button appears under its turn and does the same: it puts
  `budget` in the command box, and the player presses Enter to run it and
  refresh the pane. It's dimmed while anything is typed in the command box.

## Being away

- Closing the tab changes nothing in the game: the country keeps producing
  and the world keeps updating.
- On return after missed updates, a **While you were away** box lists the
  turns missed, messages that arrived, and headlines naming the country.
- Empire's own mailbox (`read`) holds only notices the game writes — attacks,
  sales, a deity's changes; a "[new tele]" notice in the transcript is a
  button that puts `read` in the command box.

## Other things the gateway intercepts

`change` (country name and password are managed by the site), `country` and
`players` are answered by the gateway with a message saying where the
feature went, so they don't reach Empire. `pray` isn't available.
