# Nation levels, BTUs, ETL, and population

The country-wide numbers that change slowly, update by update, and the
budgets that limit a session. This is the depth layer behind SKILL.md's
"Mobility, BTUs, and ETL" and "Food and survival" sections.

## BTUs (Bureaucratic Time Units)

The currency most commands cost (`list` shows each command's price).
Generated from the civilians in your **active capital** specifically — no
capital, no BTU income at all — and capped at `max_btus` (640 by default and
in this deployment). Per ETU, a capital makes

    min(civilians, 1000) × efficiency × work% × btu_build_rate

with efficiency × work% floored at 0.5%; a mountain capital always counts as
0.5%. `version` prints the rate as "civilians to produce a BTU in one time
unit". At the source default rate a full, healthy capital earns 640 BTUs'
worth in five or six ETUs, far less than one update, so BTUs only get tight
with a small, damaged, or starving capital. Deities and visitors are topped up to the maximum unconditionally.

Empire grants BTUs at login and at each update (`src/lib/common/btu.c`,
`grant_btus`). Playempire keeps each country's session logged in
indefinitely, so its countries get only the update grant: a country that
runs out waits for the next update, and a healthy capital refills it to the
cap then.

## ETL (Elapsed Time Limit)

A daily cap on minutes logged in (econfig `m_m_p_d`), reset at midnight
server time. A plain Empire client shows the minutes used as the first number
in its command prompt; Playempire doesn't show the prompt numbers. The
limit is a full day by default and in Playempire's games, so it is never a
real constraint there.

## ETU (Empire Time Unit) and the game clock

The game's internal clock tick. A fixed number of ETUs always separates
updates — if updates are scheduled closer together or farther apart, the ETU
itself stretches or shrinks to compensate, so formulas priced "per ETU"
(growth, production, eating) stay consistent regardless of the real-world
update schedule.

This deployment's default is 60 ETUs per update with updates 10 minutes
apart, so one ETU is 10 real seconds (`version` prints both for a given
game).

## The update

The periodic tick where the world advances. The order is fixed and matters,
because later steps see the results of earlier ones (`update/main.c` in the
server source; `info Update-sequence` describes it too):

1. **People.** Ships, planes and land units are prepared (crews eat).
   Then, sector by sector: fallout damage; guerrilla fighting; loyalty and
   revolt; taxes collected and military paid; bank interest; eating,
   starvation and population growth (which also sets each sector's work
   percentage and avail); plague. Then the reserve is paid.
2. **Units maintained.** Ships, planes and land units pay upkeep.
3. **Sectors build and produce.** Each sector pays upkeep, raises its
   efficiency (or tears down an old designation), enlistment centers enlist,
   and every sector at 60% efficiency or more produces.
4. **Units built and repaired** from what's left of each sector's avail.
5. **Goods move.** Deliveries, then distribution exports, then distribution
   imports.
6. **National levels.** BTUs granted; education and happiness move toward
   this update's output; technology and research gains (plus bleed from other
   countries) added.
7. **Decay and leak.** Technology and research lose 1% per 96 ETUs; the
   reserve shrinks; countries far behind the leader may catch up a little.
8. **Mobility** added to every sector and unit.

A country whose treasury goes below zero is on strike ("industries are on
strike"). The update keeps a running balance as it goes, so a country can go
broke partway through: from then on it skips step 3's building and
production and step 5's deliveries and distribution (`economy.md` § Taxes
and budget).

## The four national levels

Technology, research, education, and happiness (`nation` shows all four).
Each changes at every update, from points that particular sectors produce,
but they work in two different ways:

- **Technology and research accumulate.** Each update's points, after
  diminishing returns, are *added* to the level, and the level decays slowly.
  Stop producing and the level stays roughly where it was.
- **Education and happiness track current output.** Each update the level
  moves part of the way toward a target set by this update's points per
  civilian. Stop producing and the level drifts back toward 0.

- **Technology** gates what you can build: a ship, plane, land unit or nuke
  needs your tech level to be at least the type's own tech number
  (`show ship build` and its siblings list it, and `show ship build <tech>`
  lists only what that tech allows). Units are built at your current tech,
  which also lengthens gun, radar, plane and torpedo ranges. Tech raises the
  output of agribusinesses, oil fields, uranium mines and factories — every
  product except iron, gold dust and bars, and the four levels — and petrol,
  shells and guns can't be made at all below tech 20 (`economy.md` §
  Production chains). `info Technology` has the full steps.
  - *Source:* only technical centers (`t`) produce it. Each unit costs $300,
    1 gold dust, 5 oil and 10 light construction materials (where each comes
    from: `economy.md` § Production chains), and the output
    is scaled by education as `(educ-5)/(educ+5)`.
  - *Diminishing returns:* take the update's total units, subtract "easy
    tech", add 1, take the logarithm to the "tech log base", and add "easy
    tech" back. Up to the easy amount, every unit counts in full.
    `version` prints both numbers; the source defaults are 1.0 and 2.0. Past the easy amount, doubling production adds about 1.
  - *Bleed:* you also gain 1/`ally_factor` (default 2, so half) of the
    average tech gain of other countries. With the `ALL_BLEED` option on (the
    source default, and this deployment's), the average covers every country
    producing tech; off, it covers only countries you and they have both
    declared Allied.
  - *Decay:* tech falls 1% per 96 ETUs.
  - *Leak:* below one fifth of the top country's tech, each update has a
    20% chance of gaining a third of the gap to that one-fifth mark.
  - *Cost:* with the `TECH_POP` option on, each unit costs more once you
    have over 50,000 civilians (off by default).
- **Research** is made in research labs (`r`) the same way technology is
  made in technical centers: the same raw materials (but $90 a unit instead of
  $300), education scaling, bleed, decay and leak, with "easy research"
  fixed at 0.75 and the log base at 2.0 (`info Research`). It does **not** feed technology. Its effects depend on the
  game's options: with `RES_POP` on, it limits how many civilians and
  uncompensated workers fit in a sector (550 each at research 0, up to
  1,000), and with plague on (`NO_PLAGUE` off), higher research makes plague
  less likely. In the source defaults both are off, so check `version` or
  `info Options` for this game.
- **Education** is made by libraries/schools (`l`, 1 lcm and $9 per class of
  graduates). Its target each update is the update's graduates per civilian
  you founded the sectors of (`edu_cons` × graduates ÷ (civilians × ETUs)),
  with diminishing returns above 5, so a country twice the size needs twice
  the schools for the same level. The level moves toward the target slowly
  (weighted as if the old level were 192 ETUs of output). Education is what
  makes technical centers and research labs productive: their output is
  scaled by `(educ-5)/(educ+5)`, which is zero at education 5 or below.
- **Happiness** is made by parks (`p`, 1 lcm and $9 per unit) and works the
  same way, but moves faster (weighted 48 ETUs) and is worth less the more
  educated your people are: at high education, each unit counts for about
  half. Your civilians expect a minimum happiness of
  `(tech − 40)/40 + education/3`, which `nation` prints as "Happiness
  needed". Each sector has a
  **loyalty** number, 0 (loyal) to 127 (not at all), which only a deity can
  see; a player notices it through "Civil unrest" telegrams and falling work
  percentage. Below
  the needed happiness, sectors you founded can lose loyalty at each update;
  starvation lowers it too; otherwise it drifts back toward 0. A sector
  above 65 whose military is under a twentieth of its civilians loses work
  percentage and may revolt; above 70 it refuses `enlist`. A conquered sector
  starts disloyal, and becomes fully yours (as if you'd founded it) when its
  loyalty reaches 0.

## Food and population

None of this section applies when the game has `NOFOOD` on, which is this
deployment's default; `version` says.

Civilians, military and uncompensated workers each eat `eatrate` (0.0005)
units of food per ETU, so 1,000 people eat 30 units over a 60-ETU update.
Agribusinesses (`a`) grow food, scaled by the sector's fertility and
efficiency, and by technology. A sector self-sufficient in food doesn't need
deliveries; one that isn't depends on the distribution network
(`references/economy.md`) to keep it fed. Ships and land units carry and eat
food too.

**Starvation** hits any sector that can't cover its people's food at update
time. The sector first spends half its labor growing emergency rations; if
it is still short, everyone it can't feed dies, uncompensated workers first,
then civilians, then military, and never more than half the people there.
Starving also sets the sector's work percentage to 0 and makes its civilians
less loyal. The food stock won't warn you: by the update that shows zero,
the loss has already happened. The `starvation` command does, listing every
sector headed for this outcome at the next update; on Playempire the
Warnings pane runs it after each update and shows any sector it names.

## Plague

A chance, each update, of disease killing population — more likely at high
population and low research, and switched off entirely when the game has
`NO_PLAGUE` on, which is both the source default and this deployment's.
