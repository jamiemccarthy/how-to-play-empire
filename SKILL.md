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
stops there: beyond the standard first move (explore everything reachable),
it does not teach war strategy, opening theory, or how to out-think an
opponent. It does not cover the server internals, the gateway,
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
- **Enslavement and nuclear attack get a serious tone** (D-79). Elsewhere
  the game's voice can be casual, but unfree workers are enslaved people,
  and converting, shooting or nuking a population is described plainly,
  without jokes and without lecturing the player.
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
  'jjnh', movement cost 0.400`. It routes only through sectors you own, to a
  sector you own, so an `explore` into new land needs its path typed out.

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
1,000 civilians, 55 military and 75 unfree workers each. In
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
   `^`. Click a capital to see its people, food and mobility. Scroll the
   map pane down for the sector table, a sortable row of figures for every
   sector you own (`census` prints the same as text).
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

   **Explore everything you can reach, first thing after `break`.**
   Experienced players almost always open this way. On an island that means
   the whole island; on a continent, every sector within a sensible radius of
   the capitals. Land you own is land nobody else can take quietly, and it is
   where your economy will come from. Send **one military per sector**, from
   the capitals: one soldier is enough to claim and keep a sector, and it
   leaves the civilians at home working. The mobility used is per person
   moved, so moving one at a time costs little. Empire has no single command
   for "put one soldier in every sector I can see". The opening is a run of
   one-line explores, one per sector, each with its whole path typed out:

   ```
   expl m 0,0 1 gh
   expl m 0,0 1 ggh
   expl m 0,0 1 yh
   expl m 2,0 1 jh
   expl m 2,0 1 nnh
   ```

   Work outward in rings, nearest sectors first. A path may run only through
   sectors you own, so each ring is reached across the one before it. Start
   every explore from a capital rather than from newly claimed land, which
   has no mobility until the next update. The path has to be typed:
   Empire's own route-finding (`expl m 0,0 1 -2,2`) uses only sectors you
   own, so it can't reach a sector you don't own yet. On the play page the
   map draws a typed path as you type it, and the up arrow brings back the
   last line to change its final letters.
3. **`designate`** — sets what a sector you own is *for* (see Sectors,
   below); it needs no military there. Newly explored land is wilderness,
   which produces nothing; designating it a mine, an agribusiness (food), or
   another working type is what makes it start earning. It prints nothing
   when it works. A sector under 5% efficiency changes at once; a built-up
   one keeps its old letter on the map while the old buildings come down over
   the next updates, and the sector box shows the change pending as an arrow
   (`m → a`).

   **What to designate first.** Explore before choosing, then read the land:
   click a sector you own and the sector box shows its five resource ratings
   (iron `min`, `gold`, fertility `fert`, `oil`, uranium `uran`, each 0–100),
   and the sector table lists them for every sector, sorted by whichever
   column you click. Output scales with the rating, so put each designation
   where its rating is high. A sector produces nothing below 60% efficiency,
   and only its own people build efficiency, so designate the few sectors you
   can staff now (step 4) rather than everything at once. Building costs no
   materials (a fortress is the one exception) and $1 per point of
   efficiency, so $100 a sector. A good first set:

   - **Mines `m`** on the best iron, with **light and heavy manufacturing
     `j` and `k`** close by. Iron becomes lcm (1 iron each) and hcm (2 iron
     each), and every ship, plane and land unit is built from them: a
     frigate takes 30 lcm and 30 hcm, a fishing boat 25 and 15. At tech 0
     both factories run at half rate.
   - **An agribusiness `a`** on the most fertile land, if the game uses food
     (`version`; see Food and survival below). Each capital starts with 550
     food, about 15 updates of eating for its own people at 60 ETUs an
     update, and fewer once they grow.
   - **A harbor `h`** on the coast. Ships are built there, and on an island
     the next land is over the water.
   - **A gold mine `g`** where gold is high, with **a bank `b`** to turn its
     dust into gold bars. A bar costs 5 dust and $10 to make. Kept in the
     bank, each bar earns $15 an update at 60 ETUs (`bankint`, scaled by
     the bank's efficiency), so a bar pays for itself within an update and
     keeps paying. Gold, oil and uranium run out as they're mined; iron and
     fertility don't.

   **Make the rest highways `+` for now.** Each step of a move costs the
   mobility of the sector it enters: 0.4 for wilderness, 0.2 for most
   sectors at 100% efficiency, and almost nothing for a highway at 100%.
   Designating is free and a highway has no upkeep. Building one up costs
   the same $1 per point as any sector, and spare civilians get it to 100%
   in an update or two. Wilderness does nothing, so any sector you haven't
   found a use for yet is better as a highway, especially one poor in every
   resource or one that many routes pass through. When you need the land
   for something else, redesignate it.

   One command does it for every sector still wilderness. It's a good first
   use of a realm and a selector (`references/syntax-advanced.md`):

   ```
   desi # ?des=- +
   ```

   `#` is realm 0, which starts as a rectangle around the capital (8 sectors
   left, 10 right, 5 up and down). `?des=-` keeps only the sectors whose
   designation is wilderness, and `designate` skips any sector you don't
   own. On a larger island, `realm 0 <x1:x2,y1:y2>` first makes realm 0
   cover the land you hold.

   Later, `improve road` cuts a working sector's step cost to a tenth and
   leaves its designation alone. A full road network takes 200 lcm and 200
   hcm (`info improve`).

   The spare capital is the best land you have. Both starting sectors rate
   100 in every resource (Empire's default, `at_least_one_100`), so it suits
   whichever product you need most. With its 1,000 people still in it, it is
   rebuilt as the new type within about an update.

   Some designations do nothing for now:
   - Shell industries `i`, defense plants `d` and refineries `%` need tech
     20 before they make anything, and a new country has tech 0. Tech 20 is
     a long way off: in a short game, such as one between two players,
     nobody may reach it. With no shells, no gun can fire, on a frigate or
     anywhere else.
   - Technical centers `t`, research labs `r`, libraries `l` and parks `p`
     need gold dust, oil or lcm from sectors that don't exist yet, and pay
     off only over many updates.
   - Fortresses `f` defend at twice the strength of most sectors, but they
     are the one designation that takes materials: 100 hcm and $500 to reach
     100%, hcm your first ships need. A fortress fires on ships only with
     guns and shells, which need tech 20. Until then, military in the
     coastal sectors do most of its job for none of the hcm.

   `references/economy.md` has the full table of what each product needs.
4. **`move`** civilians from the capitals into the sectors you designated
   (`move c 0,0 200 nh`). Two numbers set how many:
   - **A full sector doesn't grow.** 1,000 civilians is the most an ordinary
     sector holds (a mountain holds 100), and each capital starts with
     exactly that, so neither has any births. Below the cap, civilians grow
     by 30% an update at 60 ETUs an update, if there's food for the babies.
     Moving people out of a capital lets it grow back while they work
     somewhere useful. Draw mostly on the spare capital: BTUs come from the
     active capital's civilians, though even half full it refills the BTU
     cap well within an update (`references/nation-levels.md`).
   - **About 200 makes a sector productive at the first update.** Each update
     a sector has 0.6 labor per civilian (at 60 ETUs; a soldier counts as
     0.4 of a civilian), and half of that can go into efficiency, at 1 point
     per 2 units of labor. So 200 civilians raise a new sector 60 points an
     update. It reaches 60% at the first update and produces that same
     update, then reaches 100% at the second. One soldier left by `explore`
     builds almost nothing.

   Moving civilians out of a capital is cheap. From a sector at 60%
   efficiency or more they travel packed ten to a unit, so 200 civilians
   cost what 20 soldiers would. One step into wilderness costs 8 mobility,
   and a capital gains 60 mobility an update (one per ETU), up to 127.
5. **Set up distribution** once the first sectors are designated, so goods
   and people move at each update without commands. Two commands set it up:
   - `distribute <sector> <other>` names the one sector that `<sector>`
     trades with, its **distribution sector**. The sector box shows it as
     "Distributes to …".
   - `threshold <item> <sector> <number>` sets how much of one item
     `<sector>` keeps. Below the threshold the sector pulls the shortfall
     from its distribution sector; above it, the sector sends the surplus
     there.

   **A threshold only moves goods between its own sector and that sector's
   distribution sector.** It never reaches the sectors that name this one as
   their distribution sector. So thresholds go on the sectors that make
   goods or need them, not on the sector the goods pass through. The common
   mistake is to put `threshold i <warehouse> 500` on the warehouse and
   expect the mines that distribute to it to fill it up. Nothing moves:
   each mine needs its own iron threshold (`threshold i <mine> 1`), and the
   warehouse's threshold is ignored unless the warehouse distributes
   somewhere itself.

   Most players start with one **hub**. That isn't an Empire term: it means
   a sector, usually a warehouse, that every other sector names as its
   distribution sector, so producers send to it and consumers pull from it.
   A hub isn't required, only easier and cheaper (below).

   **What happens at an update** (`update/finish.c` and `update/distribute.c`):
   - **Nothing moves when you type the commands.** Distribution runs once
     each update, almost last: after taxes and growth, production and
     building ships. The next update after you set it is the first that
     uses it.
   - **Goods go all the way in one update.** Every sector first sends its
     surplus straight to the hub along the cheapest path through land you
     own, however far that is. Then every sector draws what it's short of
     straight from the hub. All the sending happens before any drawing, so
     goods go from producer to hub to consumer in the same update. (`deliver`
     is different: it moves goods one sector per update.)
   - **Each link in a chain adds an update**, because goods are used at the
     update after they arrive. Iron mined at one update reaches the factory
     at the end of that update and becomes lcm at the next. That lcm
     reaches the harbor the same update and goes into a ship at the one
     after. Once running, every link works every update.
   - **Who pays the mobility:** the sending sector pays for what it sends to
     the hub, and **the hub pays for everything other sectors draw from
     it**. The cost is a tenth of what a `move` would be: path cost × the
     item's weight ÷ its packing ÷ 10. A sector short of mobility moves only
     what it can pay for, and one with none moves nothing. Sectors are
     handled left to right and top to bottom, so when the hub runs short,
     the sectors handled last go without. New mobility arrives after
     distribution, so the hub can spend only what it had left.
   - **Nothing moves at all while your money is below $0.**

   **Packing makes the hub matter.** Civilians travel packed ten to a unit
   from any sector at 60% efficiency or more. Goods (iron, lcm, hcm, food,
   gold dust) pack ten to a unit only in a warehouse `w` or harbor `h` at
   60% or more, and goods sent *into* one get its packing too. Drawing 200
   iron along a path costing 0.4 costs a capital hub 8 mobility, and a
   warehouse hub 0.8.

   **Choosing the hub.** Start with the active capital. It is near the
   middle of your land and holds the civilians, so `distribute * 0,0` is
   enough at first; the capital's own setting just clears. Do hand moves
   from the spare capital and leave the hub's mobility for distribution.
   Once a warehouse near the middle of your land reaches 60%, make it the
   hub: `distribute * <warehouse>`. A sector has only one distribution
   sector for every item, so new sectors then draw civilians from the
   warehouse too. Feed it by giving the capital a civilian threshold to
   keep, for example `threshold c 0,0 600`: the capital then sends its
   civilians above 600 to the warehouse at each update. A sector sends
   civilians only while its `work` is 100.

   **Setting thresholds.** A threshold is both a floor and a ceiling, set on
   the sector that holds the goods:
   - **0 means no threshold**, not "keep none". The sector never sends or
     pulls that item, and `threshold <item> <sector> 0` is how to clear one.
     The smallest working threshold is 1.
   - **Producers** get a threshold of 1 on what they make, so they send all
     but one to their distribution sector. A mine with a distribution
     sector but no iron threshold keeps everything it makes.
   - **The hub needs no thresholds** for what it collects. Goods sent to it
     arrive whatever its settings say.
   - **Consumers** get a threshold of about what they use in an update,
     plus some to spare. At 60 ETUs an update, a factory with 200
     civilians at 100% uses up to about 120 iron an update. At tech 0 it
     makes about 60 lcm or 30 hcm from that. A mine with 200 civilians at
     100% and an iron rating of 80 makes about 96 iron. So roughly one mine
     keeps one factory busy.
   - **Civilians:** `threshold c <sector> 200` keeps a designated sector
     staffed from the hub.
   - **Food**, in a game with food: give the agribusiness a threshold of 1.
     Give every other sector about three updates' eating: at 60 ETUs an
     update a person eats 0.03 food, so a sector of 200 needs 6 an update
     and a threshold of about 20.

   A first setup, with the active capital as the hub:

   ```
   distribute * 0,0        every sector trades with 0,0
   threshold i 3,1 1       the mine at 3,1 keeps 1 iron and sends the rest to 0,0
   threshold i 5,1 150     the factory at 5,1 draws up to 150 iron from 0,0
   threshold l 5,1 1       ... and sends its lcm there
   threshold c 3,1 200     the mine draws civilians from 0,0 up to 200
   threshold c 5,1 200     ... and so does the factory
   ```

   **Without a hub**, sectors can trade straight down a chain. Any sector
   can be another's distribution sector:

   ```
   distribute 3,1 5,1      the mine trades with the factory
   threshold i 3,1 1       ... and sends it all its iron but 1
   distribute 5,1 7,1      the factory trades with the harbor
   threshold l 5,1 1       ... and sends it all its lcm but 1
   ```

   The factory has no iron threshold on purpose: one of 30 would send its
   iron above 30 on to the harbor, because a threshold is a ceiling too. A
   sector that only receives an item should have no threshold for it. A
   chain gives each sector only one partner for every item, and a sector
   can pull only from its own distribution sector, which is why a hub
   holding everything is easier once there are several producers and
   consumers.

   A sector designated later needs its own `distribute` and thresholds.
   `level` shows every sector's settings, and the update telegrams report
   a sector with "No path to dist sector". `references/economy.md`
   § Distribution network has the rest of the rules and more patterns.

   **What each sector uses and makes.** At each update, every sector at 60%
   efficiency or more makes its product from its own stock and its people's
   labor. Nobody has to give an order: iron in a light manufacturing sector
   becomes lcm by itself. The early-game types:

   | Sector | Uses, per unit made | Makes | Send the output to |
   |---|---|---|---|
   | mine `m` | labor | iron, scaled by the iron rating `min`; never runs out | `j` and `k` |
   | gold mine `g` | labor | gold dust, scaled by `gold`; the deposit runs down | a bank |
   | agribusiness `a` | labor | 9 food per unit of labor, scaled by `fert`; half rate at tech 0 | every sector with people, in a game with food |
   | oil field `o` | labor | oil, scaled by the oil rating; runs down; half rate at tech 0 | nothing early; later technical centers, refineries |
   | light manufacturing `j` | 1 iron | lcm; half rate at tech 0 | the harbor |
   | heavy manufacturing `k` | 2 iron | hcm; half rate at tech 0 | the harbor |
   | bank `b` | 5 gold dust, $10 | gold bars, which earn interest while they stay there | keep them in the bank |
   | enlistment center `e` | civilians | military, up to half the civilians there | wherever soldiers are needed |
   | library `l`, park `p` | 1 lcm, $9 | education, happiness (national levels) | — |
   | technical center `t` | 1 gold dust, 5 oil, 10 lcm, $300 | technology; needs education 5 | — |
   | capital `c` | $60 upkeep an update at 60 ETUs | BTUs, from its civilians | — |
   | harbor `h`, warehouse `w` | — | nothing; goods pack ten to a unit there, and ships are built in a harbor | — |
   | highway `+`, fortress `f` | — | nothing | — |

   Labor per unit is 1 for most products, 2 for hcm, 5 for a bar and 16 for
   technology. At low tech nothing else uses lcm or hcm: land units need
   tech 30 and planes tech 50, and building a sector up needs no materials
   except for a fortress. Later uses are roads (`improve road`, 200 of each
   for a full network) and bridges (100 hcm and $1,000, at tech 10).

   **Looking it up in a game.** A game can change these tables, so check
   with `show`, which costs no BTUs:
   - `show product`: each product's inputs, money cost per unit, the
     resource it depletes, and its production rate as a formula of the
     national level, for example `(tech+10)/(tech+20)` for lcm.
   - `show sect capabilities`: which product each sector type makes, and
     its multiplier (agribusiness 900%).
   - `show sect build`: what building a sector to 100% costs, and its
     upkeep; also the per-point cost of roads and rails.
   - `show sect stats`: mobility cost, defense, packing and population cap
     by type.

   **Building things: ships, and later land units and planes.** These you
   order with `build`, and they take materials from the sector they're
   built in. Look up the costs with `show ship build` (lcm, hcm, labor,
   tech and money), `show ship cap` (what each can carry) and `show ship
   stats` (armor, speed, range); `show land` and `show plane` take the same
   `build`, `stats` and `capabilities` words. `show` lists only types your
   tech allows, or nearly allows, so at tech 0 it lists exactly the ships
   you can build:

   | Ship | lcm | hcm | Labor | $ | Carries |
   |---|---|---|---|---|---|
   | fishing boat `fb` | 25 | 15 | 75 | 180 | 300 civilians, 10 military, 900 food, 15 unfree workers; catches food at sea |
   | slave ship `ss` | 60 | 40 | 160 | 300 | 20 civilians, 80 military, 200 food, 1,200 unfree workers |
   | frigate `frg` | 30 | 30 | 110 | 600 | 60 military, 10 shells, 2 guns, 60 food; can land troops with `assault` |

   **Ships are built from the harbor's own stock.** `build` needs the harbor
   at 60% efficiency or more. It starts the ship at 20%, using a fifth of
   its lcm, hcm and labor at once, and at each update the ship builds
   further from what the harbor holds and from its people's labor. So keep
   a stock at the harbor, with the harbor distributing to the hub like any
   other sector: `threshold l <harbor> 50` and `threshold h <harbor> 50`.
6. **`budget`** — previews what the next update will earn and spend, in the
   Budget pane (1 BTU). A country whose money goes below $0 stops building
   and producing partway through the update. At 60 ETUs an update, a
   civilian pays up to $0.50 in tax, scaled by the sector's efficiency (none
   at all in a sector still at 0%), and every soldier costs $5 in pay. That
   is another reason to explore with one soldier each and to build sectors
   up quickly.
7. **Wait for an update.** Efficiency, production and mobility all move only
   at updates; the Game clock counts down to the next one.

### Every update after that

A short routine keeps a young country growing:

1. **Read what happened.** Read the turn divider in the transcript,
   telegrams in Messages, the News pane, and the Warnings pane (starvation,
   in a game with food).
2. **Run `budget`** and check money is going up, or at least not
   running out.
3. **Scan the sector table:**
   - designated sectors climbing toward 60% efficiency;
   - capitals or other sectors at 1,000 civilians, which have stopped
     growing and should send people out;
   - goods piling up somewhere they aren't used.
4. **Spend the capitals' mobility.** A sector gains 60 an update and stops
   at 127, so mobility left unspent for two updates is lost. Use it to
   explore, move civilians and move goods.
5. **Claim and designate.** Explore anything newly in reach, designate the
   sectors you can staff, and give them a `distribute` and thresholds.

That's enough to leave sanctuary, claim the land around you, point it at
something productive, and keep it growing. The rest of this skill fills in
the concepts behind those steps.

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
- **uw** — unfree workers: people who have been enslaved, most often
  conquered civilians put through `convert`. They work and eat like
  civilians and pay about a fifth of a civilian's tax.

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

In a game with food, every civilian, soldier and unfree worker in a
sector eats at each update, out of food grown by agribusinesses (`a`) and
moved to where it's needed. A sector short of food first spends half its
labor growing emergency rations; if that still isn't enough, people starve —
unfree workers first, then civilians, then military, up to half the
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
command help, first steps, messages, nation, news and relations, warnings,
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
