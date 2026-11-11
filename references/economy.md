# Economy

Sector types, designations, resources, production, the distribution network,
taxes, the market, and infrastructure. This is the depth layer behind
SKILL.md's "Sectors" and "Starter command set" sections.

## Fixed sector types

Every sector type sits on one of five underlying terrains: sea, mountain,
wasteland, plains, or wilderness. `designate` only changes a sector to
another type on the same terrain, and only a deity can designate into or out
of a fixed type, so for a player these never change:

| Symbol | Type | Notes |
|---|---|---|
| `.` | Sea | Ships only; land units cross it by bridge. |
| `^` | Mountain | Costly to move through; holds a tenth of the usual population; produces gold dust like a gold mine. Can serve as a capital in a pinch (`capital`), but generates very few BTUs. |
| `~` | Plains | Holds a tenth of the usual population; produces nothing. |
| `\` | Wasteland | Left by a nuclear blast. Uninhabitable forever. |
| `s` | Sanctuary | A new country's two starting sectors (on wilderness). Nobody can attack them; `break` turns them into capitals. |

`-` **Wilderness** is the starting state of most land, and every designation
below is built on it.

## Designations (what's built there)

Set with `designate`. Under 5% efficiency the change is immediate; above
that the old designation is torn down at the next updates before the new one
starts building from 0% (SKILL.md § Sectors). Harbors and bridge heads must
border water.

| Symbol | Designation | Symbol | Designation |
|---|---|---|---|
| `c` | Capital | `d` | Defense plant |
| `p` | Park | `i` | Shell industry |
| `+` | Highway | `m` | Mine |
| `)` | Radar station | `g` | Gold mine |
| `#` | Bridge head | `u` | Uranium mine |
| `=` | Bridge span | `h` | Harbor |
| `@` | Bridge tower | `w` | Warehouse |
| `b` | Bank | `*` | Airfield |
| `a` | Agribusiness | `o` | Oil field |
| `%` | Refinery | `j` | Light manufacturing |
| `k` | Heavy manufacturing | `f` | Fortress |
| `t` | Technical center | `r` | Research lab |
| `n` | Nuclear plant | `l` | Library / school |
| `e` | Enlistment center | `!` | Headquarters |

`show sect build` prints what it costs to bring each designation from 0 to
100% (lcm, hcm, labor and money), its upkeep, and the cost of each kind of
infrastructure; `show sect stats` prints what a
fully-efficient one of each produces or provides; `show sect capabilities`
lists what a designation lets a sector do (e.g., which ones can build ships).

A few designations do something not obvious from the name alone:

- **Highway** (`+`) — cheaper to move through than other terrain.
- **Bank** (`b`) — smelts gold dust into gold bars.
- **Enlistment center** (`e`) — converts civilians to military at each
  update, faster the more military are already there (`military.md` §
  Reserves).
- **Headquarters** (`!`) — where land units are built. Land units repair
  three times faster here (and in fortresses) than in other sectors, and at
  60% efficiency an HQ adds 1 to the reaction range of land units in it.
- **Fortress** (`f`) — fires on enemy ships in range automatically, is the
  hardest sector to take, and repairs land units as fast as a
  headquarters.

## Resources and items

Fourteen items exist, shared by sectors and units:

civilians, military, shells, guns, petrol, iron ore, gold dust, gold bars,
food, oil, light construction materials, heavy construction materials,
unfree workers, radioactive material.

`show item` prints each item's weight and other stats. A sector holds at most
9,999 of any one item.

Each sector also has five natural resource ratings, 0–100: iron (`min`),
gold (`gold`), fertility (`fert`), oil, and uranium (`uran`). In a selector,
the oil rating is `ocontent`, because `oil` there means the oil in stock. A
mine's or field's output is scaled by its rating. Gold, oil and uranium are
used up as they're extracted; iron and fertility never run down. `resource`
and `survey` report the ratings.

## Mobility

Every sector, ship, plane, and land unit has its own mobility, gaining 1 per
ETU (ships 1.5) at each update, up to 127. It is spent on:

- moving things: a sector pays to `move` or `explore` goods and people out of
  it, and pays for its distribution exports and deliveries;
- marching, sailing and flying units;
- combat;
- `improve` (sector) and `work` (engineer units);
- fortifying, laying mines, and drawing supply.

Producing goods and building up efficiency cost no mobility. A holder at
zero can't do any of the above until the next update. There's no shared pool
— a sector being mobility-starved doesn't affect a ship sitting in its
harbor.

## Production

At each update, every sector at **60% efficiency or more** whose designation
has a product makes some (`update/produce.c`). The amount is the smallest of
three limits:

- **labor:** the sector's avail × its efficiency × (for mines and fields) its
  resource rating, divided by the labor one unit takes;
- **materials:** how many units its stock of inputs covers;
- **resource:** what's left in the deposit, for gold, oil and uranium.

That amount is then multiplied by the product's rate. For most products the
rate depends on a national level, rising toward its full value as the level
climbs past a minimum, and production stops below the minimum, with a
telegram saying so. The designation can multiply it as well: an agribusiness
makes 9 food per unit of labor, a refinery 10 petrol per oil. No sector makes
more than 999 of an item in one update. `show product` prints the inputs, the
level each product depends on and its minimum; `show sect stats` prints each
designation's multiplier.

Two `census` columns describe a sector's labor, and neither is the `work`
command:

- **work** — the percentage of the sector's people working. It drops to 0
  when the sector starves and recovers by 7–21 points an update once
  everyone is fed again. A sector whose work is under 100 won't export
  civilians (see Distribution network).
- **avail** — the sector's labor at the update. Up to half of it can go
  into raising the sector's own efficiency; what's left goes to production,
  and what's left after that to building or repairing ships, planes, land
  units and nukes there.

`stop sector <SECTS>` switches a sector off for the next update: it neither
builds efficiency nor produces nor works on units there, but still pays tax
and upkeep and distributes goods (`census` shows it in the `prd` column).
Stop orders expire at the update, so a stop lasts one update unless
repeated; `start sector <SECTS>` cancels one before then. A sector you
conquer starts out stopped for the update that follows.

The `work` *command* is something else: an engineer land unit spends its own
mobility to raise a sector's efficiency directly (6 mobility per 1% at 100%
unit efficiency).

### Production chains: what each product needs and where it comes from

Raw materials are consumed from the producing sector's own stock, so each one
has to be mined there or delivered in (see Distribution network below).
Amounts per unit made, from the source defaults in `global/product.config`
(`show product` prints the table for a particular game):

| Product | Made by (designation) | Needs per unit | Money | Depends on |
|---|---|---|---|---|
| iron ore | mine `m` | — (iron rating) | — | — |
| gold dust | gold mine `g`, mountain `^` | — (gold rating, depletes) | — | — |
| oil | oil field `o` | — (oil rating, depletes) | — | tech |
| food | agribusiness `a` | — (fertility) | — | tech |
| radioactive material | uranium mine `u` | — (uranium rating, depletes) | $2 | tech, minimum 40 |
| lcm (light construction materials) | light manufacturing `j` | 1 iron | — | tech |
| hcm (heavy construction materials) | heavy manufacturing `k` | 2 iron | — | tech |
| petrol | refinery `%` | 1 oil | $1 | tech, minimum 20 |
| shells | shell industry `i` | 2 lcm, 1 hcm | $3 | tech, minimum 20 |
| guns | defense plant `d` | 1 oil, 5 lcm, 10 hcm | $30 | tech, minimum 20 |
| gold bars | bank `b` | 5 gold dust | $10 | — |
| technology | technical center `t` | 1 gold dust, 5 oil, 10 lcm | $300 | education, minimum 5 |
| research (listed as `medical`) | research lab `r` | 1 gold dust, 5 oil, 10 lcm | $90 | education, minimum 5 |
| education | library / school `l` | 1 lcm | $9 | — |
| happiness | park `p` | 1 lcm | $9 | — |

"Depends on tech" with no minimum means output starts at half rate at tech 0
and approaches full rate as tech grows. A new country has tech 0, so it
cannot make petrol, shells or guns until it has built some technology.

So a technical center needs three separate supply lines: gold dust (from
gold mines), oil (from oil fields), and lcm (from light manufacturing, which
itself eats iron from mines). The center produces nothing while any of the
three is missing. Its update telegram says how many "technological
breakthroughs" it made. Ships, planes and buildings are built from lcm, hcm,
workers and money, not directly from iron.

## Distribution network

Once things are produced, they have to move. Six commands manage this, and
they compose:

- **`distribute <SECTS> <DISTSECT>`** — name the one sector (its
  *distribution sector*; usually a warehouse, but any sector you own works)
  that the listed sectors trade goods with. A new `distribute` replaces the
  old one; `distribute <SECTS> .` clears it. The reply shows the mobility
  cost of the cheapest path, which must run entirely through sectors you own.
- **`threshold <ITEM> <SECTS> <NUMBER>`** — the stock level the sector tries
  to keep of one item. The same number is a floor and a ceiling: below it the
  sector imports from its distribution sector, above it the sector exports
  the excess. A threshold of 0 doesn't take effect — it has to be at least 1.
  Items by letter: `c` civilians, `m` military, `u` unfree workers,
  `f` food, `s` shells, `g` guns, `p` petrol, `i` iron ore, `d` gold dust,
  `b` gold bars, `o` oil, `l` lcm, `h` hcm, `r` radioactive material.
  Civilians, military and unfree workers distribute like any other
  item, with extra limits on civilians: the distribution sector never ships
  out its last civilian (or its last military, if it has no civilians); a
  sector whose `work` value (a `census` column) is under 100 does not export
  civilians; and civilians never move out of or into a sector you conquered
  rather than founded, whichever end of the trip it is.
  **A producer needs a threshold to export.** A mine with a `distribute`
  but no iron threshold sends nothing; give it a threshold of 1 (or whatever
  it should keep) and everything above that goes to the distribution sector.
- **`deliver <ITEM> <SECTS> <THRESHOLD> <DIRECTION>`** — at each update,
  push everything above the threshold one sector over, in the direction
  given (e.g., a mine's iron straight into the factory next door). It runs
  before distribution and alongside it, spends at most half the sending
  sector's mobility, and only delivers into a sector you own. Chained
  deliveries move goods one sector per update. Thresholds are stored in
  multiples of 8; a value that isn't one is rounded down.
- **`level`** — reports each sector's current `distribute`/`threshold`
  settings and stock levels.
- **`cutoff`** — reports each sector's current `deliver` thresholds and
  directions.
- **`route <ITEM> <SECTS>`** — draws each sector's `deliver` direction for
  one item as an arrow, to check a chain of deliveries at a glance.
  Distribution has no route display; `level` shows its settings.

**Order at the update.** Distribution is one of the last things an update
does, after growth, production and delivery. Every sector with a distribution
sector first exports its excess (scanning left to right, top to bottom), then
every sector imports what it is short of in the same scan order. A sector
that has no distribution sector of its own, but is named by others, is a pool
they draw from; it exports nothing. Mobility for sending *to* the
distribution sector is paid by the sender, and for taking goods *from* it by
the distribution sector; either side with no mobility left moves nothing,
and one with too little moves only what it can pay for. Warehouses and
harbors at 60% efficiency or more pack goods more tightly, so moving goods
in or out of them costs less mobility, which is why they make good
distribution sectors.

**Patterns.**
- *Hub pool:* make the stockpile sector (say a mine, or a warehouse) the
  distribution sector for everything else, and give each consumer a
  threshold. Each consumer then draws straight from the pool, independent of
  the other consumers.
- *Feeding a factory:* give the factory a `distribute` to the pool and a
  threshold on each input it needs (for a technical center: `d`, `o`, `l`).
- *Staffing a sector:* give an enlistment center (or any sector that needs
  workers) a `distribute` to a populous sector and a threshold on `c`. Both
  must be sectors you founded, and the populous one must be at 100% work.

**Military control** gates transfers out of conquered sectors only; goods
can still flow into one. A sector
you founded always counts as controlled. A sector you conquered (its old
owner is someone else) needs enough military to hold it down before it may
export, deliver, or act as the distribution sector others import from: the
check counts the sector's own military twice and adds the military in land
units there (security units again, scaled by efficiency), and needs the total
to be at least a tenth of its civilians. With no land units, that works out
to military at least a twentieth of the civilians (`subs/control.c`). An
undergarrisoned conquered sector silently stops sending goods.

**Capture clears the settings.** Taking a sector wipes its `distribute`,
`deliver` and thresholds; set them again after you take it.

## Taxes and budget

At each update, civilians and unfree workers pay tax, scaled by their
sector's efficiency (captured civilians pay a quarter); military are paid ten
times a civilian's tax, reservists a tenth of that; sectors, ships, planes
and land units have upkeep; and production and construction cost money.
With this deployment's 60 ETUs per update, a civilian in a 100% sector pays
about $0.50 an update and each military costs $5; a country starts with
$25,000. `budget` previews the next update's income and expenses (the play
screen's Budget pane shows its last output). Banks pay interest on the gold
bars they hold, scaled by the bank's efficiency: about $15 per bar per
update at 100%.
A unit's upkeep doubles while your tech is below 85% of the tech it was
built at.

A country whose treasury goes below zero is **broke** ("industries are on
strike"). The update keeps a running balance as it works through the
country, so it can go broke partway: from then on, sectors neither build
efficiency nor produce, no goods are delivered or distributed, and each ship,
plane or land unit whose upkeep can't be paid loses 12% efficiency instead
(never below its minimum). Between updates, the commands that need money
(`explore`, `build`, `attack`, `enlist`, `improve`, `demobilize` and others)
disappear from the command list until the treasury is back to zero or more,
answering `"explore" is not a legal command`; `list` marks them with `$`.

To get out: taxes still come in, so cutting costs is usually enough. `stop`
sectors whose production you don't need this update and hold off on new
building. Military pay is the biggest steady cost, but `demobilize` (which
turns military back into civilians for $5 each) is refused while broke, so
trim military while the treasury is still positive, when `budget` shows a
shortfall coming.

## Buy, sell, and the market

Only in games with the `MARKET` option on; it is off in this deployment's
games, and `buy`, `sell` and `market` refuse there. Where it's on, `sell`
puts goods from a harbor or warehouse (60% efficiency or more) up as a lot,
`buy` bids on a lot, and the highest bid when the bidding time runs out
wins. Civilians and military can't be sold. `market` lists open lots.

## Infrastructure

Each sector has three infrastructure ratings of its own, separate from its
efficiency: **road** and **rail** (which lower the mobility cost of moving
goods and units through it) and **defense** (which makes it harder to
take). `improve <road|rail> <SECTS> <NUMBER>` raises one, spending lcm,
hcm, mobility and money. Defense is turned off in this deployment's games:
`improve defense` refuses. Infrastructure survives redesignation and falls
only when the sector takes damage. `sinfrastructure` reports all three.

## Building ships, planes and land units

`build <ship|plane|land|nuke> <SECTS> <TYPE>` (each can be shortened to its
first letter; bridges are `build bridge <SECTS> <DIRECTION>`). TYPE is the
short code from the build list, not the full name: `build s 2,0 dd` for a destroyer, `build l 4,0 inf` for infantry.
`show ship build` (or `plane`, `land`, `nuke`) lists each type you can build
now, with its code, cost and tech requirement; types need up to 1.25× your
current tech before they appear at all.

- **Where:** ships in a harbor `h`, land units in headquarters `!`, planes
  in an airfield `*`, nukes in a nuclear plant `n`. The sector must be at
  least 60% efficient ("Sector … is not 60% efficient").
- **What:** the type's lcm, hcm (and for some types guns, shells, military
  or radioactive material) must be in the building sector, plus the money,
  and your tech must meet the type's requirement. A wrong type code and too
  little tech get the same reply, "You can't build that!", so check the code
  against `show ship build` (which lists only what your tech allows) before
  suspecting anything else. Missing materials or money get their own
  messages.
- **Growth:** a new ship starts at 20% efficiency, a plane or land unit at
  10%. Each update it grows, using the sector's leftover avail and more of
  the same materials, until 100%.
