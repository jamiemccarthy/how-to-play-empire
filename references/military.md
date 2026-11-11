# Military

What each class of unit is *for*, plus the mechanics around keeping them
functional — missions, fortification, supply, mine-laying, morale. This is
deliberately shallow: it stops at "here's what a plane is good for," not "how
to win an air war." Combat resolution and actual tactics are out of scope for
this skill.

Ships are built in harbors, land units in headquarters, planes in airfields,
and nukes in nuclear plants. Every unit type also has a tech level below
which it can't be built (`show land build`, `show ship build`, `show plane
build`, `show nuke build`; these list only types up to 1.25× your current
tech, and a tech number after them shows what that tech would allow). `build`
takes a type's short code from that list (`build s 2,0 dd`, not
`destroyer`); `economy.md` § Building ships, planes and land units has the
rules. A new country, at tech 0, can build only a few ships (fishing boats
and frigates among them); the first land unit needs tech 30 and the first
plane tech 50 in the source defaults, so the first military choices wait on
the first technical centers.

## Land units

The source-default types (`global/land.config`), grouped by role:

- **Infantry** — light infantry, infantry, motorized and mechanized
  infantry. General-purpose ground troops, the default for holding and
  taking territory.
- **Marines** — infantry built to land from ships (`assault`).
- **Cavalry** — the earliest unit (lowest tech), fast but light.
- **Armor** — light armor, armor and heavy armor: the main offensive punch.
- **Artillery** — light, ordinary, heavy and mechanized. Fires at a distance
  rather than closing to fight, and supports combat in range.
- **AA unit** — shoots at planes.
- **Engineers** (and mechanized engineers) — tech 130 and 260 in the source
  defaults, so a mid-game unit. Raise sector efficiency (`work`), lay land
  mines (`lmine`), help nearby units fortify, and blunt an enemy's mines.
- **Supply** and **train** — carry shells, food and other goods so units in
  the field stay supplied. A train moves only along rail.
- **Security** — tech 170. Garrison troops for holding conquered land; they
  count extra toward military control (`economy.md` § Distribution network).
- **Infiltrator** (`spy`, tech 40) and **commando** (tech 55) — spying and
  raiding units. An infiltrator is a land unit built in headquarters and can
  ride ships; it is separate from the `spy` command, which needs only
  military in the sector (`diplomacy-and-communication.md` § Scouting).
- **Radar unit** — detection rather than combat.

`show land stats` prints exact attack/defense/speed/cost numbers per type;
`show land capabilities` shows what each type can and can't do (carry cargo,
build things, etc.).

### Reserves

The reserve is one country-wide number of people (`nation` and `budget`
show it), not troops standing in a sector. Two commands move people in and
out of it:

- `demobilize <SECTS> <NUMBER>` turns military back into civilians in the
  sectors you name. It asks "New civilians on active reserve? (y/n)"; answer
  `y` and the same number is added to the reserve. It costs $5 per soldier, is
  refused while the country is broke, and skips sectors under 60% efficiency
  or that you conquered rather than founded. A negative number leaves that
  many military in each sector and demobilizes the rest.
- `enlist <SECTS> <NUMBER>` turns civilians into military, drawing them
  from the reserve. With an empty reserve it prints "No military reserves
  left" and does nothing. It works in any sector you founded that has
  civilians, not only enlistment centers. At most half a sector's civilians
  and 500 people per sector can be drafted per command, and `enlist` won't take
  a sector above 999 military. A sector whose hidden loyalty number is above
  70 (`nation-levels.md` § The four national levels, Happiness) refuses:
  "civilians refuse to report". A negative number sets a quota: `enlist *
  -100` brings each sector up to 100 military. Each sector's mobility drops
  by the fraction of its civilians drafted, and the command costs an extra
  BTU per 50 enlisted.

The only way to put people into the reserve is `demobilize` with `y`. A new
country starts with an empty reserve, so `enlist` does nothing until you
have demobilized some military into it. Demobilizing a sector's military
just to enlist them elsewhere moves troops between sectors for $5 each; if what
you want is more military overall, use enlistment centers instead.

Reservists are paid a tenth of what active military are, so holding people in
the reserve is cheap. The reserve shrinks about 1% per 24 ETUs.

**Enlistment centers** (`e` designation) work separately from the reserve.
At each update, a center you founded, at 60% efficiency or better, converts
civilians to military at $3 each, without touching the reserve. Each update
it converts up to `etus * (10 + military) * 0.05` civilians — 30 at 60 ETUs
with no military there, and 3 more for every soldier already present — and
stops once military reach half the civilians. To bring more civilians to
convert, use `distribute` plus `threshold c` (`economy.md` § Distribution
network).

## Ships

About three dozen source-default types (`global/ship.config`), from tech 0
upward:

- **Working ships** — fishing boats and trawlers (catch food at sea), cargo
  and ore ships, tankers, oil exploration boats and oil derricks (pump oil
  from the sea floor).
- **Transports** — troop transports and landing ships, for moving land units
  and putting them ashore.
- **Warships** — frigates (buildable at tech 0), patrol boats, destroyers,
  light and heavy cruisers, battleships and battlecruisers, and later
  missile, anti-aircraft and aegis cruisers. Guns fire only with shells,
  which need tech 20 to make. Until then a frigate is a transport for up to
  60 military that can put them ashore with `assault` (its `semi-land`
  flag), not a gun platform.
- **Submarines** — torpedo attack from hiding; later missile and nuclear
  subs.
- **Carriers** — light, fleet and nuclear, carrying and launching planes.
- **Mine warfare** — minesweepers clear sea mines; ships with the mine
  ability (some cruisers, destroyers, submarines) lay them with `mine`.
- **Anti-submarine** — ASW frigates and destroyers, with sonar and depth
  charges.

`show ship stats` and `show ship capabilities` give the exact numbers and
what each type can carry or do — cargo capacity varies enormously between a
destroyer and a cargo ship, for instance.

## Planes

Grouped by role rather than by era (`global/plane.config`; `show plane
capabilities` lists each type's abilities):

- **Fighters** — air-to-air; they intercept enemy planes and escort bombers.
- **Escorts** — fighters specialized for escorting.
- **Bombers** — light to heavy; strike sectors, ships and land units.
  Planes with the tactical ability also fly support and interdiction
  missions.
- **Anti-submarine planes** — hunt submarines.
- **Helicopters and transports** — carry cargo or paratroopers.
- **Reconnaissance** — scouting flights, no combat role.
- **Satellites** — launched into orbit for spying or mapping.
- **Missiles** — fly one way: anti-ship, anti-aircraft (SAMs) and
  surface-to-surface.

`show plane stats` gives exact numbers and range/cargo figures.

## Nukes

Three families — fission, fusion and neutron — differing in yield. They're
built in nuclear plants (`n`) and delivered by plane or missile; `show nuke
stats` gives exact figures. A blast can leave wasteland, a type no command
reverses, and lingering fallout: radioactive particles that spread to
nearby sectors and take a long time to clear (`info Fallout`).

## Missions

`mission <ship|plane|land> <UNITS> <mission> <op-sector> [<radius>]` gives a
unit a standing order, so it acts automatically when the situation arises.
The unit acts within a radius around its *op sector* (by default its full
range). It spends mobility only when it actually acts, and loses the mission
as soon as you move or use it for anything else.

| Mission | Who can run it | What it does |
|---|---|---|
| interdiction (`i`) | ships, land units, tactical planes | Shell or bomb anything belonging to a country you're At War with that moves in the area |
| support, offensive support, defensive support (`s`, `o`, `d`) | tactical planes | Bomb in support of your attacks (`o`), your defense (`d`), or both (`s`) |
| reserve (`r`) | land units | React to an attack on a sector in the area, fight, and return |
| escort (`e`) | escort and intercept planes | Escort mission planes taking off from the same airfield |
| air defense (`a`) | intercept planes | Intercept enemy planes anywhere in the area, not just over your own territory |

`mission ... q` shows a unit's mission; `mission ... c` clears it.

### Without a mission assigned

A unit with no mission still reacts in a few ways:

- **Return fire.** When an enemy uses `fire` on one of your sectors, ships or
  units, the target fires back if it has guns and shells, and your other
  forts, ships and artillery in range join in (an ally's too, if the ally is
  also at war with the attacker).
- **Forts** fire automatically at enemy ships that sail into range.
- **Interceptors** — planes with the intercept ability, on no mission, rise
  against planes of any country you're Hostile to or At War with that fly
  over your sectors, ships or land units, within half their range. Neutral
  and Friendly planes are reported but not intercepted.

Land units react to attacks only when on a reserve mission.

### Plane range: `range`

`range <PLANES> <range>` caps how far a plane will fly to react or carry out
a mission, which is useful for keeping an expensive interceptor, ABM or SAM
covering a small area instead of roaming.

Missiles are the one gotcha: they fly at **double** their set range, since
they don't need to fly back. A missile meant to reach 6 sectors out needs
`range <missile> 3`, not 6.

## Attacking spends mobility and BTUs

`attack` and `assault` cost 3 BTUs each, like any other command, and the
units that take part also spend their own mobility. In a `BLITZ` game BTUs
never run out, so mobility is what limits how fast combat can go (SKILL.md §
Mobility, BTUs, and ETL).

## Fortify, supply, and mines

- **`fortify <UNITS> <MOBILITY>`** — a land unit turns mobility into
  fortification, point for point (50% more with an engineer in the sector).
  A fully fortified unit is twice as hard to hurt and twice as strong on
  defense. It loses all fortification if it moves or retreats.
- **`supply <UNITS>`** — a unit draws its basic load (a turn's food and one
  firing's worth of shells) from nearby supply sources. A unit without it is
  "out of supply".
- **`lmine <UNIT> <NUMBER>`** — an engineer turns shells into land mines in
  its sector, 1 mobility per mine. Mines give the sector's defender a bonus;
  engineers with an attacker reduce it.

## Morale

A land unit's **retreat percentage** decides how readily it breaks off when
hit in combat: the chance of retreating is the retreat percentage minus the
unit's efficiency. New units start at `morale_base` (42 by default).
`morale <UNITS> <PERCENT>` changes it, up to 100%, and down to a minimum of
`morale_base` minus your happiness, so a happier country can order units to
stand and fight longer.
