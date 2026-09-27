# Datapack and mod integration

Wanderer's Discovery loads custom discovery definitions from server data. A mod
can bundle a definition in its JAR, or a player can install the same files as a
world datapack.

For a mod, place the files under `src/main/resources/data/`. For a datapack,
place `pack.mcmeta` and `data/` at the pack root, then install that directory
under `<world>/datapacks/`. The example in this repository includes the pack
metadata for Minecraft 26.2. Install Wanderer's Discovery and Fabric API on both
the client and server. Minecraft 26.2 requires Java 25.

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
    "fallback": "TEMPLE DISCOVERED"
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
Changing the definition ID or the order or number of variants can change that
selection. These definitions describe existing structures; they do not generate
or register a structure.

## Fields

| Field | Default | Purpose |
| --- | --- | --- |
| `structures` | none | Structure IDs or `#structure_tag` entries |
| `structure_tags` | none | Additional structure tag IDs |
| `priority` | `2` | Lower values win when multiple definitions match the same structure |
| `detection` | `piece` | `piece` or `bounding_box` |
| `suppress_cave_ambience` | `false` | Optional client integration state described below |
| `name` | file name | Display name translation and fallback |
| `heading` | location discovered | Heading translation and fallback |
| `name_generator` | none | Use `wanderers_discovery:village` for generated names |
| `map_icon` | `minecraft:filled_map` | Item ID exposed to compatible maps |
| `banner_texture` | standard banner | Namespaced 256x44 GUI texture |
| `sound` | generic sound | One sound ID or sound object |
| `sounds` | generic sound | Deterministic sound variants |
| `advancement` | none | Advancement awarded after the presentation |
| `uses_vanilla_advancement` | `false` | Prevents manual awarding when another system owns it |

Each sound object accepts `event`, `duration_ticks`, `glow_peak_tick`, `volume`
and `pitch`. `duration_ticks` also controls the presentation lifetime and custom
advancement delay. At least one structure or structure tag is required.

Targets are alternatives: matching any listed structure or tag is sufficient.
Equal priorities are resolved by definition ID in alphabetical order. A custom
definition takes precedence over a built-in category. Separate overlapping
structures can each be discovered; their presentations are queued.

For a custom sound object, `event` is required. The remaining defaults are:

| Field | Default | Accepted range |
| --- | --- | --- |
| `duration_ticks` | `200` | Minimum `60` |
| `glow_peak_tick` | `70` | Clamped to the sound duration; the HUD further limits it to the fully visible interval |
| `volume` | `0.24` | `0` to `4` |
| `pitch` | `1.0` | `0.01` to `4` |

At normal game speed, 20 ticks equal one second. Set timing to match the audio;
the mod does not measure the file duration. A sound ID string uses these same
defaults. When both `sounds` and `sound` are present, `sounds` takes precedence.
An omitted or empty sound list uses the built-in generic presentation instead
(257 ticks, requested glow peak at tick 43).

For manually awarded advancements, use a dedicated `minecraft:impossible`
criterion as shown in the example. All criteria in the referenced advancement
are awarded after the presentation, with a short additional delay.
`uses_vanilla_advancement: true` disables that manual award; it does not make
the discovery wait for an advancement or trigger from one.

### Optional ambience integration signal

`suppress_cave_ambience` is an optional state exposed through the client API.
Wanderer's Discovery does not stop or modify ambient sounds itself. A consuming
client integration may use the value while the player is inside the discovery.
The field defaults to `false` and has no standalone effect.

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
This is the only supported generator ID in 1.0.1. It takes precedence over
`name`; arbitrary generator IDs do not register new generators.

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

Supply a 256x44 PNG for a custom banner and keep the central text area clear.
Both opaque and transparent artwork are supported. Omitting `banner_texture`
selects the standard fallback banner; a missing custom texture is not
automatically replaced. The complete definition above references two sound
variants, so supply both sound events and audio files.

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
`ruined_portal`, `ancient_city`, `trail_ruins` and `trial_chambers`.

A full custom definition takes precedence over built-in matching. Vanilla
desert wells use a dedicated placed-feature detector and cannot be extended
through a structure tag.

## Optional client API

`DiscoveryClientApi.mapMarkers()` returns immutable map-ready markers containing
a stable ID, discovery type ID, dimension, coordinates, localized display name
and map icon. Register a listener through
`DiscoveryMapEvents.MARKERS_CHANGED` to refresh a map after joining a world or
discovering a location.

These classes belong to `net.fabs.wanderersdiscovery.api` and are client APIs.
Wanderer's Discovery does not include a map UI. `map_icon` is a marker hint for
a consuming map integration; the example uses an existing Minecraft item ID.
The map integration determines how to draw it. Without a compatible map, this
field has no visible effect.

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

Use `/datapack list enabled` to check installation. Reload server definitions
with `/reload`; reload client assets with F3+T after editing a resource pack.
When distributing a ZIP, put `pack.mcmeta` at the ZIP root rather than inside
an extra enclosing directory. Test changes in a disposable world: changing an
ID leaves the old discovery record in that world's history.

Definitions and discovered coordinates are stored server-side per world and
player in multiplayer. Integrated singleplayer shares discoveries within its
world.
