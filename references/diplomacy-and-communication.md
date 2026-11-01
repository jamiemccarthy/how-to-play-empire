# Diplomacy and communication

Relations, the message commands, and the reporting commands that tell you
what's happening in the world. This is the depth layer behind SKILL.md's
"Communication and diplomacy" section.

## Relations

Five levels, in a fixed order that comparisons rely on:

**Allied > Friendly > Neutral > Hostile > At War**

Set with `declare` (`declare <alliance|friendly|neutrality|hostile|war>
<country|*>`); the play page's News and relations pane shows both
directions for each country (`relations` prints the same as text).
Relations are **one-directional** — what you declare toward another country
is independent of what they've declared toward you, so you can be Friendly
toward a country that has declared War on you. Where a rule below depends on
a relation, it says whose declaration counts.

What each level means, mechanically:

- **Allied** — your forts, ships and artillery fire in support of a
  country you've declared Allied when it fights a country you've declared At
  War (both conditions are your declarations); its planes can land at your
  airfields and on your carriers; it can fly over your territory without
  triggering a warning (and without you being told); the Messages pane's
  *your allies* reaches every country you've declared Allied;
  with the `ALL_BLEED` option off, you share in the technology and research
  of countries you and they have both declared Allied. `ALL_BLEED` is on by
  default, and in this deployment, which shares everyone's gains with
  everyone regardless of relations (`nation-levels.md` § Technology). Allied
  implies Friendly trade relations too.
- **Friendly** — a friendly country's ships may enter your harbors, load and
  unload goods, and get repaired there; you may `tend` their ships. You're
  still told when they overfly you, and each overflight can drop your
  relation to Hostile (below).
- **Neutral** — the starting relation toward every country. A spy of theirs
  caught in your land is deported.
- **Hostile** — your interceptors attack that country's planes over your
  territory, your forts fire on its ships in range, and your anti-satellite
  weapons, ABMs and anti-missile ships go after its satellites and missiles;
  a spy of theirs caught in your land is shot.
- **At War** — your units on interdiction and support missions act against
  that country.

**Attacking needs no declaration.** `attack`, `assault`, bombing and
shelling all work against a country you're Neutral or even Allied toward;
the relation decides only how *your* automatic defenses treat *them*.

**Relations drop on their own.** When another country does something hostile
to you — takes or shells one of your sectors, overflies you, shoots one of
your spies — there is a chance your relation toward it drops to Hostile, from
whatever it was (Allied included). The chance grows with how hostile the act
was and how many sectors it touched: about 5% per sector overflown, 20% per
sector taken. Overflights by a country you have declared Allied aren't
reported at all, so they never cause a drop. Nothing automatic ever reaches At War, and nothing automatic
ever raises a relation.

## Messages between players

In Playempire, players write to each other in the website's **Messages**
pane (`playempire-screen.md` § Sidebar panes). Empire's own `telegram`,
`announce`, `flash`, `wall` and `wire` commands are intercepted, and the
gateway answers with a pointer to the pane. Relations don't gate messages; a
player can block another country from the pane, which stops messages both
ways. Messages are stored by the site, not end-to-end encrypted, and the
site's admins can read any of them; players shouldn't put real-world
passwords or personal secrets in one.

Empire's mailbox still exists: **`read`** shows the notices the game itself
writes — the update's production report, attacks on your land, spies caught,
a deity's changes. Only the game writes there, since players' messages go
through the website.

`reject` still runs, but what it blocks (Empire telegrams and announcements
from a country) no longer carries player messages; use the pane's block
instead.

## Reporting and intelligence

- **`report`** — each active country's rough tech, research, education and
  happiness, shown as a range relative to your own levels. Countries still in
  sanctuary don't appear.
- **`news`** / **`headlines`** — the game's public event log: battles,
  territory changes, declarations, and other notable happenings visible to
  everyone. The play screen's **News and relations** pane shows the newest
  headlines.
- **`relations`** — what each side has declared, both directions; the News
  and relations pane already shows it.
- **`power`** — relative strength rankings across all countries, a coarse
  measure of who's strong without revealing their internals.
- **`nation`** — your own country's summary (see `references/nation-levels.md`
  for what the four levels shown there mean).

`declare` and `reject` cost 2 BTUs, `relations`, `news` and `report` cost
nothing; `list` shows every command's cost.

## Scouting

Empire tells a country nothing about land it doesn't own beyond the type
character on the map (`?` for another country's sector). To learn what's in
it, look:

- **`spy <SECTS>`** — from each of your sectors that holds military, looks
  into the six neighboring sectors you don't own and reports each one's
  designation, owner, and figures rounded to the nearest 10: efficiency,
  civilians, military, shells, guns, petrol, food and gold bars, plus any
  land units and planes there. Costs 1 BTU, plus one more for each 40
  sectors in the area named, and needs an active capital. It reaches only
  neighboring land, so it can't see across water. Each look risks being
  caught, with a chance of the target sector's military divided by 200 (100
  military: a 50% chance):
  if the target's owner has declared you Neutral the spy is deported, if
  Hostile or At War the spy is shot (you lose one military, and it makes the
  news), and Friendly or Allied owners never catch one. A caught spy reports
  nothing unless a recon land unit is in the sector it came from.
- **`lookout <SHIPS>`** and **`llookout <UNITS>`** — a ship or land unit
  reports the sectors, ships and units it can see around it. 1 BTU.
- **`coastwatch <SECTS>`** — coastal sectors report ships in sight; range
  grows with sector efficiency and tech, and radar installations see much
  farther. **`skywatch`** does the same for satellites, **`sonar`** from
  ships for submarines.
- **`recon`** — sends spy planes along a path to report what they overfly.
  Needs an airfield and planes, so it's later in a game.

What a look reveals is remembered on your map, and may go out of date.

## Realms and territory

- **`realm`** — saves a numbered rectangle of coordinates so a frequently
  referenced area can be typed as a short name (e.g. `#1`) instead of full
  coordinates every time. Per-country, managed entirely by the `realm`
  command.
- **`territory`** — sets a number from 1 to 99 on each sector you name, a
  label of your own choosing that shows in the `ter` column of `census`. It
  has no effect on the game; it's for picking sectors out later with a
  selector (`census * ?terr=27`). There are four separate label fields,
  `terr` and `terr1`–`terr3` (`territory <SECTS> <NUMBER> 1` sets `terr1`),
  so one sector can carry, say, an island number and a role number at once.

## Change and name (a common point of confusion)

- **`change country`** / **`change representative`** — in Empire, these
  rename your country or change its *representative*, the password it logs
  in with. Playempire logs in for the player with both, so it refuses
  `change` with a message saying the website manages them.
- **`name`** — an entirely different command: it names a **ship** (`name
  <SHIPS> <NAME>`), not the player or the country. `name <SHIPS> ~` clears
  the names of those ships.
