# Datapack and mod integration

Wanderer's Discovery loads custom discovery definitions from server data. A mod
can bundle a definition in its JAR, or a player can install the same files as a
world datapack.

## Minimal integration

Create this file in your mod or datapack:

`data/<namespace>/wanderers_discovery/poi_types/<id>.json`

```json
{
  "structures": ["examplemod:moon_temple"]
}
```

The definition above uses the standard banner and sound. The file path creates
the persistent discovery ID `<namespace>:<id>`. Do not rename a published
definition unless you intend existing worlds to treat it as a new discovery.

## Complete integration

```json
{
  "structures": [
    "examplemod:moon_temple",
    "#examplemod:moon_temples"
  ],
  "structure_tags": ["examplemod:ancient_lunar_structures"],
  "priority": 1,
  "detection": "piece",
  "suppress_cave_ambience": true,
  "name": {
    "translate": "poi.examplemod.moon_temple",
    "fallback": "Moon Temple"
  },
  "heading": {
    "translate": "hud.examplemod.temple_discovered",
    "fallback": "Location discovered"
  },
  "map_icon": "examplemod:moon_temple_icon",
  "banner_texture": "examplemod:textures/gui/moon_discovery.png",
  "sounds": [
    {
      "event": "examplemod:discovery.moon_temple_1",
      "duration_ticks": 180,
      "glow_peak_tick": 62,
      "volume": 0.24,
      "pitch": 1.0
    },
    {
      "event": "examplemod:discovery.moon_temple_2",
      "duration_ticks": 156,
      "glow_peak_tick": 48,
      "volume": 0.22,
      "pitch": 1.0
    }
  ],
  "advancement": "examplemod:exploration/moon_temple",
  "uses_vanilla_advancement": false
}
```

Sound variants are selected deterministically from the structure location. The
same structure therefore keeps the same variant after restarting the game.

## Fields

| Field | Default | Purpose |
| --- | --- | --- |
| `structures` | none | Structure IDs or `#structure_tag` entries |
| `structure_tags` | none | Additional structure tag IDs |
| `priority` | `2` | Lower values win when definitions overlap |
| `detection` | `piece` | `piece` or `bounding_box` |
| `suppress_cave_ambience` | `false` | Optional Natural Ambience integration described below |
| `name` | file name | Display name translation and fallback |
| `heading` | location discovered | Heading translation and fallback |
| `name_generator` | none | Stable generated-name provider |
| `map_icon` | `minecraft:filled_map` | Item ID exposed to compatible maps |
| `banner_texture` | standard banner | Namespaced 256x44 GUI texture |
| `sound` | generic sound | One sound ID or sound object |
| `sounds` | generic sound | Deterministic sound variants |
| `advancement` | none | Advancement awarded after the presentation |
| `uses_vanilla_advancement` | `false` | Prevents manual awarding when another system owns it |

Each sound object accepts `event`, `duration_ticks`, `glow_peak_tick`, `volume`
and `pitch`. `duration_ticks` also controls the presentation lifetime and custom
advancement delay. At least one structure or structure tag is required.

### Optional Natural Ambience integration

`suppress_cave_ambience` is not a vanilla Minecraft feature and is not required
by Wanderer's Discovery. It is only a signal exposed through the client API;
Wanderer's Discovery does not stop or modify cave sounds itself. When set to
`true`, the separate Natural Ambience mod or another compatible ambience mod
can suppress its own natural cave loops and cave events while the player is
inside this discovery. The field defaults to `false` and has no effect when no
compatible ambience mod is installed.

Use `true` for enclosed structures that should not sound like natural caves,
such as mansions or temples. Leave it `false` for genuinely underground
locations such as mineshafts, strongholds and cave ruins.

## Random village names

Use the public generator `wanderers_discovery:village` with bounding-box
detection:

```json
{
  "structures": ["examplemod:moon_village"],
  "detection": "bounding_box",
  "name_generator": "wanderers_discovery:village",
  "heading": {
    "translate": "hud.wanderers_discovery.village_discovered",
    "fallback": "VILLAGE DISCOVERED"
  }
}
```

Generated names depend on the structure location and remain stable across
sessions.

## Assets supplied by a mod

A datapack contains server data only. Custom client assets must be supplied by
a mod or a matching resource pack:

```text
assets/examplemod/lang/en_us.json
assets/examplemod/sounds.json
assets/examplemod/sounds/discovery/moon_temple_1.ogg
assets/examplemod/textures/gui/moon_discovery.png
data/examplemod/advancement/exploration/moon_temple.json
data/examplemod/wanderers_discovery/poi_types/moon_temple.json
```

Example `sounds.json` entry:

```json
{
  "discovery.moon_temple_1": {
    "sounds": ["examplemod:discovery/moon_temple_1"]
  }
}
```

The fallback text is shown when a language does not contain the requested
translation key. A standalone datapack can use the default Wanderer's Discovery
presentation or reference vanilla assets without a resource pack.

## Built-in behavior through structure tags

For exact built-in behavior, add your structure to a public tag such as:

`data/wanderers_discovery/tags/worldgen/structure/discovery/village.json`

```json
{
  "replace": false,
  "values": ["examplemod:moon_village"]
}
```

Supported paths are `village`, `pillager_outpost`, `mineshaft`,
`woodland_mansion`, `jungle_temple`, `desert_pyramid`, `igloo`, `shipwreck`,
`swamp_hut`, `stronghold`, `ocean_monument`, `ocean_ruins`, `nether_fortress`,
`nether_fossil`, `end_city`, `buried_treasure`, `bastion_remnant`,
`ruined_portal`, `ancient_city`, `trail_ruins`, `trial_chambers` and
`abandoned_camp`.

A full custom definition takes precedence over built-in matching. Vanilla
desert wells use a dedicated placed-feature detector and cannot be extended
through a structure tag.

## Optional client API

`DiscoveryClientApi.mapMarkers()` returns immutable map-ready markers containing
a stable ID, discovery type ID, dimension, coordinates, localized display name
and map icon. Register a listener through
`DiscoveryMapEvents.MARKERS_CHANGED` to refresh a map after joining a world or
discovering a location.

`DiscoveryClientApi.currentDiscoveryId()` returns the active discovery type.
`DiscoveryClientApi.suppressesCaveAmbience()` reports whether the active
location publishes the optional cave-ambience suppression signal. The consuming
ambience mod decides whether and how to react to it. Use a Fabric Loader
mod-presence check or reflection if Wanderer's Discovery is optional.

## Testing and troubleshooting

1. Run `/reload` after changing a definition.
2. Confirm the log contains `Loaded <count> custom discovery type(s)`.
3. Use `/locate structure <namespace:id>` to find the target.
4. Teleport nearby and walk into the structure instead of testing from far
   above it.
5. Use `bounding_box` when the full structure bounds should count; use `piece`
   when empty space between pieces must not count.
6. If an already discovered structure does not show again, use a new world or
   temporarily change the JSON file name. Persistence is working as intended.
7. Verify that custom sounds and textures are installed on the client.

Definitions and discovered coordinates are stored server-side per world and
player in multiplayer. Integrated singleplayer shares discoveries within its
world.
