---
name: how-to-play-empire
description: >
  How to play Empire, the multiplayer strategy game, as it runs on
  Playempire: sectors and designations, mobility, BTUs and the update, the
  economy, what each military unit is for, diplomacy, and the commands a new
  player needs. Also covers Playempire's play page: its panes, map buttons,
  sector box, Send/Answer button and messages. Use it to answer any player's
  question about how the game or the play page works, to play a country, or
  to write player-facing docs, even when Empire isn't named. Not for
  strategy or server internals.
---

# How to play Empire

Empire is a persistent strategy game: one shared world, all countries playing
at once in real time, advancing on a clock rather than in turns. There's no
tutorial and no campaign — a new country starts with two sectors and has to
build an economy, a military, and a foreign policy out of it, one command at a
time. Fifty years of players have produced fifty years of jargon, and the
game's own `info` pages read like manpages written by insiders for insiders.
This skill is the alternative: a straight path from "never seen this game" to
"can play a passable game," in the order a first session actually needs it.

**Scope.** This is about the game as a player experiences it — what things
mean, what commands do, how to read the output. It teaches enough about
combat, production, and diplomacy to make sound day-to-day decisions, and
stops there: it does not teach war strategy, opening theory, or how to
out-think an opponent. It does not cover the server internals, the gateway,
or anything below the command line a player types.

## Answering a player

Most uses of this skill are a new player's question. What has worked:

- **Point to the page first.** Playempire's play page reads the game for the
  player and keeps it on screen: the map, every owned sector's figures (click
  it), the `nation` figures and unit tables, the `starvation` warnings,
  recent headlines and both sides' relations, refreshed after each command
  and each update (`references/playempire-screen.md`). For "how do I see X",
  name the place on the page; the command that prints the same thing as text
  is a footnote, worth it for details the page doesn't show or for picking
  sectors out with a selector.
- **"Not a legal command" is a diagnosis.** Empire gives that reply for a
  misspelled command, but also whenever the country lacks what the command
  needs: still in sanctuary, broke (treasury below $0), or without an active
  capital. Work out which before suggesting anything else.
- **Check what this game has switched on.** Food, the market and a few other
  mechanics differ per game (§ Game options differ). Before giving a number
  or a warning that depends on one, say which setting it assumes, or point
  the player to `version`.
- **Most "nothing happened" answers are the update.** Efficiency,
  production, growth, mobility and BTUs all change only at updates; a sector
  makes nothing below 60% efficiency. Check those before looking for a bug.
- **Say what words mean.** A new player doesn't know "BTU", "mobility" or
  "designate"; give the plain meaning the first time a term appears.
- When the skill doesn't settle a question, read the server source
  (§ Checking a fact against the server's source) and say which file the
  answer came from.

## The world

The world is a grid of hexagonal sectors. Every sector has a **sector type**,
shown on the map as one character. Most land starts as wilderness (`-`), and
an owner turns wilderness into something useful with the `designate` command:
a capital (`c`), a mine (`m`), a harbor (`h`), an airfield (`*`), and two
dozen more (the full list is in `references/economy.md`). Choosing a sector's
type this way is called *designating* it, and the type it was given is its
*designation*.

A few types are fixed: no player command can designate a sector into them or
out of them.

| Type | What it is |
|---|---|
| sea (`.`) | Ships only; land units cross it by bridge. |
| mountain (`^`) | Expensive to move through, holds a tenth of the usual population, and produces gold dust. |
| plains (`~`) | Holds a tenth of the usual population and produces nothing. |
| wasteland (`\`) | What a nuclear blast leaves. Uninhabitable for good. |
| sanctuary (`s`) | A new country's starting land. Nobody can attack it; `break` turns it into capitals. |

A designation takes time to change: a sector that is already built up has to
be torn down over the next update or two before the new type takes over (see
Sectors, below).

**Coordinates are per-country.** There's no single "true" grid reference —
each country sees the world from its own origin, normally its first capital
at `0,0` (the `origin` command moves it). The same physical sector has a
different name for every player looking at it. Not every `x,y` pair is a
sector, either — only ones where `x+y` is even exist, a side effect of
squashing the hex grid onto a rectangular coordinate system.

**Movement** uses six direction letters, laid out for a hex grid — there's no
plain north/south, only up/down paired with left/right — plus two more:

```
y    u
 \  /
g -- -- j
 /  \
b    n
```

| Key | Direction / action |
|---|---|
| `y` | up-left |
| `u` | up-right |
| `g` | left |
| `j` | right |
| `b` | down-left |
| `n` | down-right |
| `h` | halt (end of movement) |
| `v` | view current sector |
| `m` | map around the current sector |

Those letters show up in three places, and a new player often takes them
for noise:

- **Typed as a path.** `move c 0,0 20 jjn` moves 20 civilians from 0,0
  right, right, down-right. A path can end with `h`, or simply stop.
- **The movement prompt.** Leave the path off and `move` or `explore`
  prompts for it a step at a time, drawing the sector you're in and its six
  neighbors and asking `<127.0: c 0,0>` — mobility left, the sector's type,
  its coordinates. Answer with direction letters, `v` to look at the sector,
  `m` for a map, `h` to stop there.
- **Printed back.** Give a destination instead of a path (`move c 0,0 20
  2,-2`) and Empire picks the cheapest route and prints it: `Using best path
  'jjnh', movement cost 0.400`.

On the play page the map is always in view and clicking a sector shows its
figures; while you're typing a sector into a command, clicking a cell fills
in its coordinates instead.

**What you can see.** Your map shows land you own in full, land in sight of
your sectors as just its type character (another country's land as `?`), and
land you saw before as you last saw it. The rest is blank until you
get close. In this deployment each country starts on its own island of about
30 sectors, the same for every country, with a few small unowned islands
around, so early on the map is mostly empty and other players are over the
water. To learn more about land you don't own, see
`references/diplomacy-and-communication.md` § Scouting.

## Your first session

A new country starts with **two sanctuary sectors**, side by side, holding
1,000 civilians, 55 military and 75 uncompensated workers each. In
sanctuary nobody can attack you, and nothing happens either: your people
don't eat, work or grow, and most commands — `move`, `map`, `designate`,
`budget`, `spy` among them — answer `"move" is not a legal command` until you
leave. Looking commands like `census`, `nation` and `show` work. `break` ends
sanctuary for good and turns both sanctuaries into capitals. Only one capital
is active at a time — the one your BTUs come from, shown in `nation` (the
first one, unless you switch with the `capital` command). The other is a
spare: useful as a backup if the active one is captured, otherwise just a
sector, so a common opening is to redesignate it right away. Don't
redesignate the *active* one by mistake ("You have redesignated your
capital!"): once the change goes through you lose your BTU income and the
commands that need a capital, until you designate it back to `c` or make the
other one active with `capital`. There's no going back from `break`, so it is
the one command worth pausing on before you type it.

After breaking:

1. **Look around.** The map pane shows your two capitals and the land in
   sight of them: open wilderness `-` to expand into, water `.`, mountains
   `^`. Click a capital to see its people, food and mobility. (The First
   steps checklist lists `census` and `map`, which print the same things as
   text in the transcript.)
2. **`explore`** — moves civilians or military out of a sector you own along
   a path, into unowned land, and claims the sector where they stop. It costs
   the starting sector's mobility, per person moved. This is how territory
   grows early on. A newly claimed sector starts with 0 mobility, so nothing
   moves out of it until the next update, and it counts as founded by you, so
   it needs no garrison. Civilians and military claim land alike; civilians
   then work fully and pay tax, while a soldier does the work of 0.4 of a
   civilian and costs ten times a civilian's tax in pay, and only civilians
   breed. A sector left with no civilians, military or land units of yours
   is no longer yours (`move` asks before it empties one). `explore` needs
   an active capital and a treasury of $0 or more; without either it answers
   `"explore" is not a legal command`.
3. **`designate`** — sets what a sector you own is *for* (see Sectors,
   below); it needs no military there. Newly explored land is wilderness,
   which produces nothing; designating it a mine, an agribusiness (food), or
   another working type is what makes it start earning. It prints nothing
   when it works. A sector under 5% efficiency changes at once; a built-up
   one keeps its old letter on the map while the old buildings come down over
   the next updates, and the sector box shows the change pending as an arrow
   (`m → a`).
4. **Wait for an update.** Efficiency, production and mobility all move only
   at updates; the Game clock counts down to the next one.

That's enough to leave sanctuary, see what you have, claim more land, and
start pointing sectors at something productive. The rest of this skill fills
in the concepts behind those steps.

## Specifying sectors and units

A command can take a single coordinate, but most commands accept more
than one sector or unit at a time:

| Form | Means | Example |
|---|---|---|
| `x,y` | One sector or unit | `census 2,4` |
| `x1:x2,y1:y2` | A rectangle of sectors | `census -3:3,-3:3` |
| `@x,y:dist` | Every sector within `dist` steps of `x,y` | `census @0,0:5` |
| `*` | Every sector or unit the command applies to | `census *` |

`references/syntax-basics.md` has worked examples of each. Beyond these
four, `references/syntax-advanced.md` covers numbered realms (`#1`),
unit-number lists (`3/7/12`), fleet/army/wing group letters, and
**selectors** — a filter condition (`?des=h&mil<5`) appended to narrow any of
the above down to matching sectors or units.

## Sectors: type, designation, and efficiency

Wilderness can be designated as any working type; a harbor or bridge head
must also border water. A designated sector can be
redesignated to another working type later, but never into or out of the
fixed types above. What each designation produces is in
`references/economy.md`.

**Efficiency** is a 0–100% rating that a sector builds up at each update,
and it scales nearly everything the designation does. **A sector produces
nothing at all below 60% efficiency**; from 60% up, output scales with
efficiency, so a mine at 80% produces four-fifths of what it would at 100%.
Building it up costs money, uses the sector's spare labor (the **avail** column of
`census`), and for some designations uses construction materials. It
doesn't fall from neglect; combat and bombing damage it, and redesignating
tears it down. A sector under 5% efficiency changes type at once and starts
at 0%. Above that, the old designation is torn down over the following
updates (about four times faster than building it) before the new one starts
from 0%. `show sect build` prints what each designation costs to build.

## People: civ, mil, uw

Every sector's population is one of three items:

- **civ** — civilians. They pay taxes, work, and grow (military don't
  breed), and their loyalty depends on your country's happiness level (see
  `references/nation-levels.md`).
- **mil** — military. They cost ten times what a civilian pays in tax. In a
  sector you *conquered*, they also hold it down: with too few military
  there, the sector won't send goods out by delivery or distribution, and
  disloyal civilians may revolt. Sectors you founded yourself need no
  garrison for this.
- **uw** — uncompensated workers. They work and eat like civilians and pay
  about a fifth of a civilian's tax.

## Mobility, BTUs, and ETL — the three budgets

Three separate resources limit what you can do, and new players often
conflate them:

- **Mobility** is a per-holder budget — every sector, ship, plane, and land
  unit holds its own and spends it on moving things, fighting, fortifying,
  and the like. A sector's mobility pays for moving goods and people out of
  it, including by `explore` and distribution; producing goods costs none.
  Each holder gains 1 per ETU (ships 1.5) up to 127, granted at the update
  (unless the game turns on `MOB_ACCESS`, which this deployment doesn't). Run
  out on one unit mid-session and it's stuck until the next update, no matter
  how much else you have.
- **BTUs (Bureaucratic Time Units)** are a per-country budget that most
  *commands* cost — 1 for `move` or `designate`, 3 for `attack`, 0 for
  looking at things. It's Empire's throttle on how much you, the player, can
  issue in a session. They're generated by your active capital's civilians;
  `list` shows what each command costs.
- **ETL (Elapsed Time Limit)** is a per-country cap on minutes logged in per
  day.

In this deployment BTUs arrive **at each update**: Empire grants them at
login and at each update, and Playempire keeps every country's session logged
in, so the login grant never comes. A full, working capital earns
far more than the 640 cap in one update, so a country that runs out mid-turn
is back to full at the next update; a capital with few civilians, low
efficiency or low work percentage earns less (`references/nation-levels.md`
has the formula). The play screen shows the count beside the Send button.

Talking has none of these limits. Players write to each other in the play
screen's Messages pane, which costs no BTUs and no mobility, so diplomacy and
trash talk happen as fast as you can type (§ Communication and diplomacy).

**A `BLITZ` game only fixes one of the three.** The server's `BLITZ` option
(distinct from an update schedule that just runs fast, which some games
also call blitz) refills your BTUs whenever a command would
run you short, but mobility is a separate budget the option doesn't touch. So
two players can attack each other as fast as they can type only up to
whatever mobility their units currently have; once that's spent, further
attacks with those units wait for the next update regardless of BTUs or
typing speed.

## Food and survival

Whether people eat at all is a game option. With `NOFOOD` on, nobody needs
food and starvation never happens; it is **on by default in this deployment's
games**, and `version` says which way a game is set.

In a game with food, every civilian, soldier and uncompensated worker in a
sector eats at each update, out of food grown by agribusinesses (`a`) and
moved to where it's needed. A sector short of food first spends half its
labor growing emergency rations; if that still isn't enough, people starve —
uncompensated workers first, then civilians, then military, up to half the
sector's people. Starving also drops the sector's work percentage to 0, so it
produces almost nothing for several updates afterward. Keep an eye on food
stockpiles the way you'd keep an eye on money — running out isn't a warning,
it's already the loss. In a game with food, the play page's Warnings pane
shows Empire's `starvation` report after each update, naming every sector
headed for trouble before the next one hits. `references/nation-levels.md`
has the exact rates.

## Game options differ — check `version`

A game's deity can switch whole mechanics on or off, and this deployment's
games are set up differently from the source defaults on a few. As of
2026-11-01 (from Playempire's game configuration; the in-game `version`
command is authoritative for a particular game):

| Option | This deployment | Effect |
|---|---|---|
| `NOFOOD` | on unless the admin turns food on | No eating, no starvation |
| `BLITZ` | off unless the admin turns it on | Unlimited BTUs |
| `MARKET` | off | `buy`, `sell` and `market` refuse |
| `NO_PLAGUE` | on | No plague |
| `ALL_BLEED` | on | Tech and research gains leak from every country, not just allies |
| `MOB_ACCESS` | off | Mobility arrives only at the update |
| `RES_POP`, `TECH_POP` | off | Research doesn't limit population; tech cost doesn't grow with population |

Each update is 60 ETUs by default, and the admin sets how many real minutes
apart updates run (10 by default).

## Starter command set

Everything above, as commands, plus the handful of others a first session
actually reaches for:

| Command | Does |
|---|---|
| `break` | Leave sanctuary. One-way. |
| `census` | Your sectors and their contents as a text table (the map and sector box show the same figures); `census x,y` for one. Shows columns the sector box doesn't, such as `work` and `avail`. |
| `map` | Draw the sectors around a location as text; the map pane already does this. |
| `move` | Move goods or people between your own sectors. |
| `budget` | Preview the next update's income and expenses (1 BTU). |
| `explore` | Move civilians or military into nearby unowned land to claim it. |
| `designate` | Set a sector's designation. |
| `build` | Construct a ship, plane, land unit, or nuke. |
| `work` | Spend an engineer land unit's mobility to raise a sector's efficiency faster than the update would. |
| `show` | Look up reference tables — sector build costs, item stats, ship/plane/land-unit stats, product formulas. Doesn't touch your own data; it's the in-game manual. |
| `nation` | Your country's summary: capital, tech, research, education, happiness, money, reserve. The Nation pane shows the same figures. Free (0 BTU), as are `census` and `map`. |
| `version` | Server version and a few global constants (like the tech factor). |

`show` is worth learning early and often — `show sect build`, `show ship
stats`, `show item`, and so on print exact numeric tables for anything you're
about to build or produce, straight from the server rather than from memory.
Unit tables list only types up to 1.25 times your current tech, so at tech 0
`show ship build` shows just the few tech-0 ships.

## Communication and diplomacy

Empire is multiplayer, and most of what happens to you happens because of
another country's decisions.

**Writing to other players happens on the website, not in Empire.** The
gateway intercepts Empire's own `telegram`, `announce`, `flash`, `wall` and
`wire` and answers by pointing to the play screen's **Messages** pane, where a
player writes to *a country*, *every country*, or *your allies* (the countries
you've declared Allied). Relations don't limit who can be written to;
blocking another player does, both ways (`references/playempire-screen.md` §
Sidebar panes). Messages aren't end-to-end encrypted, and the site's admins
can read them, so tell players not to put real-world passwords or personal
secrets in one. Empire's `read` still works, but its mailbox holds only
notices the game itself writes: attacks on you, a deity's changes.

The game-side toolkit:

| Command | Does |
|---|---|
| `declare` | Set your relation toward another country (2 BTUs). It tells them, and the news reports it. |
| `relations` | Each country's relation toward you and yours toward it. |
| `report` | Each country out of sanctuary: its status and a rough range for its tech, research, education and happiness. |
| `news` / `headlines` | The game's public event log. |
| `power` | Relative strength rankings across all countries. |
| `spy` | Look into the enemy sectors next to yours (`references/diplomacy-and-communication.md` § Scouting). |

**Relations** run Allied > Friendly > Neutral > Hostile > At War, and they're
one-directional — what you declare about another country isn't what they've
declared about you, and each rule depends on one side's declaration. Declaring
a country Friendly lets its ships use your harbors; Allied lets its planes
land at your airfields and overfly you unreported, and brings your forts' and
units' fire to its side against anyone you've declared At War; Hostile makes your
interceptors and forts engage it; War sets your interdiction and support
missions against it. Nothing enforces a declaration: attacking needs none, so
an ally can attack you without changing its relation first. Full mechanical
effects of each level are in `references/diplomacy-and-communication.md`.

## The play screen

Players use Empire through a web page: a sidebar of panes (game clock,
command help, messages, nation, news and relations, first steps, warnings,
budget), a map with a sector box beside it, the transcript of Empire's
output, and a command box. The site fills most panes itself with reads that
cost no BTUs; the Budget pane waits for the player to run `budget`, because
the site never spends a player's BTUs. For any question about a pane, a
button, the map's colors or an empty box, read
`references/playempire-screen.md`.

## Where this skill stops, and where to go next

This skill gets you to a working economy and a country that can talk to its
neighbors. Two things are deliberately shallow:

- **Military.** `references/military.md` explains what each class of ship,
  plane, land unit, and nuke is *for* — "here's what planes are good for,"
  not "here's how to win an air war." Combat resolution, fortification
  timing, and actual tactics are out of scope.
- **The economy in depth** — full production formulas, the distribution
  network (`distribute`/`threshold`/`deliver`), taxes, the market — is in
  `references/economy.md`.

| Question | Read |
|---|---|
| Sector types, designations, resources, production, distribution, taxes, market, infrastructure | `references/economy.md` |
| BTU/ETL exact numbers, tech/research/education/happiness formulas, food/starvation rates, plague | `references/nation-levels.md` |
| What each ship/plane/land-unit/nuke class is good for, missions, fortify/supply/mine/reserve basics | `references/military.md` |
| The website's play screen: panes, map buttons and colors, the sector box, Send/Answer, messages, being away | `references/playempire-screen.md` |
| Relations/declare, scouting and spies, news/report/power, realms and territory | `references/diplomacy-and-communication.md` |
| Rectangle/radius/wildcard sector targeting, worked examples | `references/syntax-basics.md` |
| Realm shorthand, unit-number lists, fleet/army/wing group letters, selectors | `references/syntax-advanced.md` |

The game's own manual (`info <topic>`) is the exhaustive original
documentation on anything this skill compresses — longer than a first read
needs. In this deployment the gateway doesn't pass `info` to the server: typing
it at the game prompt shows the topic in the play screen's help pane, and the
whole manual is on the site at <https://playempire.net/manual>. The troff
sources are in the server repository's `info/` directory (below).

## Checking a fact against the server's source

The game server is open source. A copy is at
<https://github.com/jamiemccarthy/empserver-docker>
(`git clone https://github.com/jamiemccarthy/empserver-docker.git`); clone it
somewhere outside this project if you don't already have it. When a question is
about exactly what happens — a number, an order of events, why a command
refused or silently did nothing — and this skill or the `info` pages are
vague, read the code. The `info` pages are mostly right but leave out
conditions the code enforces: only the code says that a sector with `work`
under 100 will not export civilians.

Paths below are under `src/lib/` in that repository, except `info/`.

| Question | Look at |
|---|---|
| What an update does, and in what order | `update/main.c` (`update_main` calls every step in order), then the step itself: `prepare.c` (taxes, eating), `human.c` (food, starvation, growth, work), `populace.c` (loyalty, revolt), `sect.c` (efficiency, enlistment centers), `produce.c` (production), `deliver.c`, `distribute.c`, `nat.c` (the four levels), `age.c` (decay, leak, reserve), `mobility.c`, `plague.c`. `../server/update.c` only schedules updates |
| Why a command refused, what it costs, what it prompts for | `commands/*.c`, named by the command's first four letters or so: `thre.c` (`threshold`), `dist.c` (`distribute`), `enli.c` (`enlist`) |
| Product inputs, per-unit money cost, which resource is depleted | `global/product.config` (the table `show product` prints; for example the `tech` row is 1 gold dust, 5 oil, 10 lcm, $300, and `medical`, the research product, is the same inputs at $90) |
| Sector build cost, mobility cost, what each designation makes | `global/sect.config` |
| Ship, plane, land-unit and nuke stats | `global/ship.config`, `plane.config`, `land.config`, `nuke.config` |
| Server options (`BLITZ`, `ALL_BLEED`, `RES_POP`) and numeric constants | `common/conftab.c`, `global/constants.c`, `global/options.c` |
| Military control, BTU and happiness helpers | `subs/control.c` (military control), `common/btu.c`, `common/hap_fact.c` |
| The manual text itself | `info/*.t` (troff source; `Products.t`, `Sector-types.t`, `Technology.t` are the economy ones) |

Habits that make this quick:

- `grep -n` for the item or constant name (`I_CIVIL`, `SCT_ENLIST`,
  `sct_dist`) and read only the function around the hit.
- A condition written `if (...) continue;` or `return` inside a loop is a
  silent skip; list them, because each one is a way a command "worked" but did
  nothing.
- The code shows what the server does, not what a particular game has
  switched on. Options and constants can differ per game: confirm with
  `version` and `show` in the game before telling a player a number.
- Say in the answer that you checked the source, and which file, so the
  next reader knows how far to trust it. If the code and this skill disagree,
  fix the skill.
