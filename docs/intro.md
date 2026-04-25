# Getting Started

If the old hosted documentation URL is unavailable, use this page + `genesis/init.lua` API comments as the source of truth.

## 1) Install Genesis

- Wally package: <https://wally.run/package/triankl3/genesis?version=1.0.0>
- Roblox model: <https://www.roblox.com/library/15536843454/Genesis-Library>
- Or copy/build from the `genesis/` folder in this repository.

## 2) Server-only requirement

Genesis must run on the server. Requiring/calling it from a LocalScript will throw.

```lua
local Genesis = require(game.ServerScriptService:WaitForChild("Genesis"))
```

## 3) Prepare prefab assets

Pass a `Folder` containing models used for object placement.

Each model should:
- be named exactly as referenced by prefab config `asset`,
- have `PrimaryPart` set,
- include any children referenced by `rbxProperties`, `texture.otherChildren`, etc.

## 4) Create a map

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Genesis = require(game.ServerScriptService:WaitForChild("Genesis"))
local assetContainer = ReplicatedStorage:WaitForChild("Assets")

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
        spikes = nil,
        prefabs = nil,
        objects = nil,
    },
    size = 120,
    seed = 12345,
}, assetContainer)

print("objects:", debugStats.total.objects)
print("time:", debugStats.time.total)
```

## 5) Optional: prefab configuration

```lua
prefabs = {
    CrystalA = {
        asset = "Crystal1",
        scale = NumberRange.new(0.6, 1.4),
        randomRotation = true,
        useNormal = true,
        bury = 0.2,
    },
    CrystalB = {
        clone = "CrystalA",
        asset = "Crystal2",
    },
}
```

Supported prefab fields are documented via the `PrefabConfig` type in the API.

## 6) Destroy/regenerate

`CreateMap` replaces any existing generated map. You can also manually clear everything:

```lua
Genesis:DestroyMap()
```

## Demo reference

Use the demo for a complete production-style setup:
- `demo/server/init.server.lua`
- `demo/server/staticConfig.lua`
- `demo/assets/`
