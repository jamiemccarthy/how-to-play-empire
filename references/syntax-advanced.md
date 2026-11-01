# Advanced targeting: realms, unit lists, groups, and selectors

Beyond the four basic forms in `references/syntax-basics.md`, Empire's
command parser supports naming a saved area, listing specific unit numbers,
grouping units under a letter, and filtering any of the above with a
condition. These are worth knowing but aren't needed for a first session.

## Realms: `#n`

A **realm** is a rectangle of coordinates saved under a country-specific
number, set with `realm <number> <SECTS>` (see
`references/diplomacy-and-communication.md`). Once saved, `#1` stands in for
that rectangle anywhere a command would take one; `#` alone abbreviates
`#0`, the first realm. A new country's realms all start out as the same
default rectangle around its capital, so `map #` works from the first
session, and `realm` with just a number shows what that realm covers.

## Unit-number lists: `n1/n2/n3`

Slash-separated ship, plane, or land-unit numbers target exactly those
units, e.g. `3/7/12`. Where a command takes a second, optional list of units
(the escorts in `bomb`, `fly` and `recon`), a lone `/` there means *no
units*: it fills the slot, so the arguments after it still line up, and the
command reports "No escorts...".

## Group letters: `army`, `wing`, `fleet`

`army`, `wing`, and `fleet` assign a single letter to a set of land units,
planes, or ships respectively, so the whole set can be referred to by that
letter instead of listing every number. `fleet a 4/7/12` puts ships 4, 7,
and 12 under fleet letter `a`; afterward, `ship a` means all three. More
than one unit can share a letter, and `~` is the default letter for anything
not explicitly assigned.

## Selectors: `?cond&cond&...`

A selector appended to a target filters it down to items matching every
condition, each written `<value><op><value>` with no spaces. Either side can
be a field name, a number, a designation letter, or a unit type:

| Operator | Means |
|---|---|
| `=` | equal |
| `#` | not equal |
| `<` | less than |
| `>` | greater than |

Multiple conditions combine with `&` (AND). Field names can be shortened to
any unambiguous prefix (`mob` for `mobil`, `own` for `owner`). Sectors have
`des` (designation), `effic`, `mobil`, `oldown`, `owner`, the item names
(`civ`, `mil`, `gun`, `food`…), the resource ratings, and more; ships, planes
and land units have `type`, `effic`, `mobil`, `tech`, their group letter
(`fleet`, `wing`, `army`), and the items they carry. Some fields, such as a
sector's loyalty, are visible only to a deity. The full lists are in
`info Selector`.

Examples:

- `census * ?oldown=own` — sectors whose old owner is their owner: ones you
  founded, or conquered ones that have become fully yours.
- `census * ?oldown#own` — sectors you conquered that are still disloyal.
- `ship * ?food=0` — ships carrying no food.
- `sect # ?mil>5` — sectors in realm 0 with more than 5 military.
- `cen * ?des=h&lcm<100` — harbors with under 100 light construction
  material.
- `census -3:3,-3:3 ?des=+&gun=5&mil>civ` — highways near your capital with
  exactly 5 guns and more military than civilians.
- `ship ~ ?type=dd&mil>5` — destroyers with more than 5 military, not
  currently in any fleet (the `~` targets ships still on the default
  letter). `type` matches the start of the type's listing (`dd   destroyer`),
  so use the short code from `show ship build`; the full name matches
  nothing.
