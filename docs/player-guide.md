# Player guide

## What the mod does

Wanderer's Discovery recognizes supported structures while the player explores
them. A first discovery shows a structure-specific presentation banner, fades
in its assigned ambient sound and records a map-ready marker. A short completion
cue plays when the presentation ends. The location remains known after leaving
the world, restarting the game or restarting a dedicated server.

Returning to an already discovered location does not replay the first-discovery
banner or sound. Its compact location label can still appear while the player is
inside. In multiplayer, discoveries are stored per player and per world. An
integrated singleplayer world uses one shared discovery history for that world.

## Built-in locations

The following vanilla locations are supported without a datapack:

| Overworld | Nether and End |
| --- | --- |
| Villages | Nether fortresses |
| Pillager outposts | Nether fossils |
| Mineshafts | Bastion remnants |
| Woodland mansions | Ruined portals in the Nether |
| Jungle temples | End cities |
| Desert pyramids | |
| Igloos | |
| Shipwrecks | |
| Swamp huts | |
| Strongholds | |
| Ocean monuments | |
| Ocean ruins | |
| Buried treasure | |
| Ruined portals | |
| Ancient cities | |
| Trail ruins | |
| Trial chambers | |
| Abandoned camps (Minecraft 26.3) | |
| Desert wells | |

Overlapping locations are recorded independently. If two new structures are
detected together, their presentation banners are queued rather than drawn on
top of each other.

## Context-sensitive presentations

Villages use one of two daytime themes during the day and one of two quieter
nighttime themes at night. Other built-in locations use audio and banner designs
matched to their structure category.

## Advancements

Wanderer's Discovery includes an exploration advancement category for its
built-in discoveries. Presentations appear immediately; mod-owned advancement
toasts are delayed until the associated discovery presentation has finished so
the two interfaces do not obscure each other. Every built-in discovery
advancement is also announced in chat. Vanilla advancements retain their normal
Minecraft timing.

## Integration support

Other mods can consume the public client marker API and the current-location
state. Wanderer's Discovery does not add a standalone minimap or world map.

## Testing a discovery

Use `/locate structure <namespace:id>`, teleport near the result and walk into
the structure. Teleporting directly into its center can make the advancement and
presentation appear immediately. Previously recorded locations intentionally do
not replay; use another structure or a disposable test world for repeated tests.
