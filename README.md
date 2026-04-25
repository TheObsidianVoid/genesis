# Genesis

Procedural generation of 3D Perlin-noise maps in Roblox.

Genesis generates:
- voxel terrain,
- material layers,
- optional spikes,
- and prefab/object placement driven by surface probes.

---

## Install

### Option 1: Wally (recommended)
Package: [`triankl3/genesis@1.0.0`](https://wally.run/package/triankl3/genesis?version=1.0.0)

### Option 2: Roblox model
Model: [Genesis Library](https://www.roblox.com/library/15536843454/Genesis-Library)

### Option 3: Source / Rojo
Use `genesis/` directly in your place (same structure as this repository's demo).

---

## Quick Start

### 1) Require Genesis on the **server**
Genesis validates server-only usage and will error if called on the client.

```lua
local ServerScriptService = game:GetService("ServerScriptService")
local Genesis = require(ServerScriptService:WaitForChild("Genesis"))
```

### 2) Prepare an asset container
Create a `Folder` that contains your prefab models. Each model should have:
- a `PrimaryPart` set,
- child parts configured the way your prefab config expects.

Example location used by the demo:
```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local assetContainer = ReplicatedStorage:WaitForChild("Assets")
```

### 3) Call `CreateMap`

```lua
local debugStats = Genesis:CreateMap({
    generatorConfig = {
        generator = "Perlin3D",
        terrain = {
            minDensity = -0.05,
            frequency = 0.04,
            verticalScale = 0.7,
            outlineMinDensity = -0.01,
            outlineFrequency = 0.08,
            falloffStart = 0.8,
            alternativeMaterialChance = 0.3,
            noiseMaterialMinDensity = 0.25,
            noiseMaterialFrequency = 0.15,
            objectMaterialMinDensity = 0.35,
            objectMaterialFrequency = 0.1,
            objectProbeChance = 0.4,
        },
        material = {
            primaryMaterial = Enum.Material.Rock,
            alternativeMaterial = Enum.Material.Slate,
            noiseMaterial = Enum.Material.Ice,
            objectMaterial = Enum.Material.Grass,
            primaryMaterialColor = Color3.fromRGB(50, 77, 85),
            alternativeMaterialColor = Color3.fromRGB(98, 206, 254),
            noiseMaterialColor = Color3.fromRGB(229, 253, 248),
            objectMaterialColor = Color3.fromRGB(101, 198, 33),
        },
        spikes = nil, -- or spike config table
        prefabs = nil, -- or prefab config table
        objects = nil, -- or weighted object placement table
    },
    size = 120,
    seed = 12345,
}, assetContainer)

print(debugStats.time.total, debugStats.total.objects)
```

> `CreateMap` destroys any previously generated map before creating a new one.

---

## Minimal Prefab Setup

`prefabs` is a dictionary keyed by prefab name.

```lua
prefabs = {
    RockA = {
        asset = "Rock1",          -- model name in assetContainer
        scale = NumberRange.new(0.8, 1.5),
        randomRotation = true,
        useNormal = true,
        bury = 0.25,
    },
    RockB = {
        clone = "RockA",          -- inherit and override
        asset = "Rock2",
    },
}
```

Helpful fields supported by prefab configs include:
- `clone`
- `asset`
- `rbxProperties`
- `proximity`
- `scale`
- `stretch`
- `randomRotation`
- `bury`
- `useNormal`
- `sound`
- `decal`
- `texture`

For complete type definitions, see `genesis/init.lua`.

---

## API Overview

### `Genesis.UseFlatMaterials` (boolean)
- Default: `true`
- When `true`, Genesis mounts built-in flat material variants during map creation.
- Set to `false` if you want to manage your own terrain material variants.

### `Genesis:CreateMap(mapConfig, assetContainer) -> debugStats`
- Generates terrain + objects into `workspace`.
- Creates a folder named `GenesisMap`.
- Returns timing and count stats.

### `Genesis:DestroyMap()`
- Destroys generated map container.
- Clears terrain.
- Removes mounted material variants.

---

## Demo Project

A full working example is included:
- server map creation flow: `demo/server/init.server.lua`
- terrain/object configuration: `demo/server/staticConfig.lua`
- source assets: `demo/assets/`

You can also play/edit the demo place directly on Roblox:
[Genesis Demo](https://www.roblox.com/games/15529154687/Genesis-Demo)

---

## Local docs

The original hosted docs URL may not be available anymore.

This repository includes Moonwave docs source in `docs/` (starting with `docs/intro.md`) and API docs generated from comments in `genesis/init.lua`.
