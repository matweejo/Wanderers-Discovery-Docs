# Wanderer's Discovery

Wanderer's Discovery is a Fabric mod that turns Minecraft structures into
persistent discoveries. Entering a new location presents a structure-specific
banner and ambient sound, records the location for the current world and awards
an exploration advancement where appropriate.

Each supported location has its own visual identity and contextual audio.
Villages receive persistent generated names, discoveries are announced in chat,
and returning players can still see a compact location label without replaying
the original presentation.

This repository contains the public user and integration documentation. It does
not contain the mod source code or distributable assets.

## Documentation

- [Player guide and built-in locations](docs/player-guide.md)
- [Datapack and mod integration](docs/integration.md)
- [Ready-to-use example datapack](examples/custom-discovery-datapack)
- [Changelog](CHANGELOG.md)

## Requirements

| Mod version | Minecraft | Fabric Loader | Fabric API |
| --- | --- | --- | --- |
| 1.0.1 | 26.3 | 0.19.5 or newer | 0.160.6+26.3 or compatible |
| 1.0.1 | 26.2 | 0.19.3 or newer | 0.157.0+26.2 or compatible |

Java 25 or newer is required for both supported Minecraft versions.

Install Wanderer's Discovery and Fabric API on both the client and server.

## Integration overview

Mods and datapacks can add discovery presentations without a Java dependency.
An integration can select structures or structure tags and customize the name,
heading, deterministic sound variants, banner, map icon, advancement, detection
mode and an optional ambience-compatibility signal.

Definitions are loaded from:

```text
data/<namespace>/wanderers_discovery/poi_types/<id>.json
```

The definition path becomes a persistent ID saved in world data. Keep it stable
after publishing.

## License

The documentation and examples in this repository are available under the MIT
License. This license applies only to this repository, not to the Wanderer's
Discovery mod, its code, sounds, branding or visual assets.

The distributed mod is covered separately by the
[Wanderer's Discovery License 1.0](MOD-LICENSE.md).
