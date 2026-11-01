# Specifying sectors and units

Most commands take a location or a unit as an argument, and almost all of
them accept more than a single coordinate or number in that slot. This is
the depth layer behind SKILL.md's "Specifying sectors and units" section —
the four forms every player uses. `references/syntax-advanced.md` covers the
forms that go beyond this: named realms, unit-number lists, group letters,
and selectors.

## One sector or unit: `x,y`

A single coordinate pair. `census 2,4` shows just that sector.

## A rectangle: `x1:x2,y1:y2`

Low and high bounds on each axis, giving every sector in that rectangle.
`census -3:3,-3:3` covers every sector from -3 to 3 on both axes around
your origin. Only coordinates whose x+y is even are sectors, so that is
25 of the 49 pairs.

## A radius: `@x,y:dist`

Every sector within `dist` steps of `x,y`, radiating outward rather than
forming a rectangle. `census @0,0:5` covers everything within 5 sectors of
your capital, regardless of shape.

## Everything: `*`

Every sector or unit the command could apply to. `census *` lists your whole
country; `ship *` lists every ship you own.

These four forms compose with each other only loosely — a rectangle or
radius stands in for the coordinate part of a command, and `*` replaces the
whole targeting argument. Which forms a given command accepts is shown by
`info <command>` in-game; the shapes above are consistent across commands
that take a location, but not every command takes a location at all.
