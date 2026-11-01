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
  **Command help**; **Messages**; **Nation**; **News and relations**;
  **First steps** (until finished or dismissed); **Warnings**; **Budget**.
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
  or `census` gives the numbers. The choice is remembered in the browser and
  sends nothing to the game. Then zoom out, zoom in, **Show capital**, and
  open the map full-size. The map scrolls; there are no pan buttons.
- **Clicking a sector** selects it and fills the **sector box** beside the
  map:
  - a sector the player owns: coordinates, designation (a pending change
    shows as an arrow, `m → a`), efficiency, mobility, people, food, any other
    goods, and the ground's resource ratings, plus a **Designate** button that
    puts `designate x,y ` in the command box (the player still sends it);
  - a sector in sight but not owned: its coordinates and "In sight", nothing
    more — Empire gives a country no figures for land it doesn't own (`spy`
    and scouting are how to learn more; `diplomacy-and-communication.md` §
    Scouting);
  - a remembered or never-seen sector: coordinates only.
- While a command is being typed and the next thing it needs is a sector,
  clicking a cell types that sector's coordinates into the command instead of
  selecting it (for `move` and `explore`, it fills in the best route there).
  The mouse pointer over the map shows which a click will do.
- Coordinates in the transcript are links: clicking one selects that sector
  on the map.
- Typing direction letters as a command's path (`move c 0,0 10 jjn`) draws the
  route on the map as you type, and the command help pane shows its mobility
  cost.

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
  tables of its ships, planes, land units and nukes. The site fills it
  itself from free reads, so it needs no command and costs no BTUs; it's
  empty while the country is in sanctuary. `nation` at the prompt prints the
  same figures, also free.
- **News and relations** — the newest eight world headlines, and for each
  other country the relation each side has declared ("you neutral, they
  hostile"). Blank until `break`.
- **First steps** — a checklist for a new country: `break`, `census`, `map`,
  `explore`, `designate`. Clicking a command name puts it in the command box;
  the player sends it. Steps tick off as they're done; order is suggested,
  not enforced, except that nothing else works until `break`. The `census`
  and `map` steps print as text what the map and sector box already show.
  **Dismiss** hides it.
- **Warnings** — the latest `starvation` report when it names a sector that
  will starve; "(none)" otherwise.
- **Budget** — the player's most recent `budget` output, laid out as income,
  expenses and the estimated new treasury. It says "(unknown - command
  budget)" until the player runs `budget`, which costs 1 BTU, so the site
  never runs it for them; the word `budget` there is a button that fills the
  command box.

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
