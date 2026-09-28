# Wanderer's Discovery integration guide

Official public documentation maintained by
[Wake The Wild](https://github.com/Wake-The-Wild).

This repository contains the public integration documentation and examples for
Wanderer's Discovery. It does not contain the mod's source code or distributable
assets.

Wanderer's Discovery allows mods and datapacks to add discovery presentations
for custom Minecraft structures without a Java dependency. Integrations can
control the displayed name, heading, sounds, banner, map icon, advancement,
detection mode and an optional ambience-compatibility signal. The ambience
signal does not change sounds by itself; Natural Ambience or another compatible
mod may choose to consume it.

## Start here

- [Datapack and mod integration](docs/integration.md)
- [Ready-to-use example datapack](examples/custom-discovery-datapack)

## Supported version

| Wanderer's Discovery | Minecraft | Fabric Loader |
| --- | --- | --- |
| 1.0.1 | 26.3 | 0.19.5 or newer |
| 1.0.0 | 26.2 | 0.19.3 or newer |

The integration format is data-driven. Keep the definition path stable after
publishing because it becomes the persistent discovery type ID stored in world
data.

## License

The documentation and examples in this repository are available under the MIT
License. This license applies only to this documentation repository, not to the
Wanderer's Discovery mod, its code, sounds or visual assets.

The mod itself is governed by the
[Wake The Wild License](https://github.com/Wake-The-Wild/WTW-License).

Questions about integration may be submitted through this repository's issue
tracker. Permission requests must be sent to
**wakethewild.contact@gmail.com**.
