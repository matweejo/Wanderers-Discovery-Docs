# Moon Temple example

This example demonstrates how a second mod can integrate one of its structures
with Wanderer's Discovery. The `examplemod` namespace represents that external
mod. A vanilla jungle temple is used as the target only so the example can be
tested without installing another real content mod.

Install Wanderer's Discovery and Fabric API, then copy this directory into a
test world's `datapacks` directory and run `/reload`. Confirm it appears in
`/datapack list enabled`. Run `/locate structure minecraft:jungle_pyramid`,
teleport nearby and walk inside. The pack changes the discovery presentation;
it does not add or generate a new temple.

The jungle temple will be presented as Moon Temple with a custom heading,
deterministic sound variants, glow timing and a custom challenge advancement.
The amethyst map icon is metadata for compatible map integrations. Wanderer's
Discovery itself does not display a map.

The JSON also sets `suppress_cave_ambience` to `true`. This is an optional state
available through the Wanderer's Discovery client API. The mod does not disable
ambient sounds by itself, and the field has no effect without a consuming
integration. Its default value is `false`.

In a real integration, replace `minecraft:jungle_pyramid` with the other mod's
structure ID and replace the `examplemod` namespace with that mod's actual ID.

The sound events and banner used here are included in Wanderer's Discovery,
so this example needs no additional resource pack. To supply your own assets,
follow the [integration guide](../../docs/integration.md).

After discovering the temple, leave and rejoin the world. The discovery banner
and sound should not replay; the location label should appear while inside.
Use a fresh test world to repeat the first-discovery test.
