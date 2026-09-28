# Moon Temple example

This example demonstrates how a second mod can integrate one of its structures
with Wanderer's Discovery. The `examplemod` namespace represents that external
mod. A vanilla jungle temple is used as the target only so the example can be
tested without installing another real content mod.

Copy this directory into a world's `datapacks` directory and run `/reload`.
Locate a `minecraft:jungle_pyramid`, teleport nearby and walk inside.

The jungle temple will be presented as Moon Temple with a custom heading,
deterministic sound variants, glow timing, an amethyst map icon and a custom
challenge advancement.

The JSON also sets `suppress_cave_ambience` to `true`. This is only an optional
compatibility signal for the separate Natural Ambience mod or another ambience
mod using the Wanderer's Discovery client API. Wanderer's Discovery does not
disable cave sounds on its own, and the field has no effect when no compatible
ambience mod is installed. Its default value is `false`.

In a real integration, replace `minecraft:jungle_pyramid` with the other mod's
structure ID and replace the `examplemod` namespace with that mod's actual ID.
