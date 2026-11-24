# Void Bastion

**A swarm-scale real-time strategy game built with Unity DOTS.**

Void Bastion is a fleet-command game about surviving and destroying an evolving enemy swarm. Players assemble a roster of ships, configure their weapons and systems, choose a route through a procedurally generated starmap, and command their fleet through increasingly difficult encounters.

The project was created by [Kean Lai](https://github.com/JamesLee5182) and [Chan Tze Yuon](https://github.com/TzeYuon) for **NUS Orbital 2026 (Apollo)**. Its main technical goal was to simulate large numbers of enemies and projectiles while keeping targeting, pathfinding, combat and visual effects responsive.

## Download

[Download the latest Windows or macOS build from GitHub Releases](https://github.com/TzeYuon/void-bastion/releases).

Choose the archive for your operating system, extract it, then launch the included executable or application. The builds are unsigned student-project releases, so the operating system may display a security warning.

## Feature showcase

### Swarm simulation at scale

Enemy pressure is represented by an increasingly dense swarm rather than only by larger health and damage values. Enemies navigate toward player ships, separate from nearby units and react to changing terrain while large numbers of projectiles and visual effects remain active.

The simulation uses Unity ECS with jobified, Burst-friendly systems. A shared [flow field](Assets/Scripts/Gameplay/Terrain/FlowFieldSystem.cs) provides navigation directions across the map, avoiding the cost of generating a complete path for every enemy. ECS [heatmap grids](Assets/Scripts/Gameplay/Spatial%20Partitioning/HeatmapUpdateSystem.cs) partition the battlefield so targeting, collision and damage systems can query nearby entities without comparing every possible pair.

### Configurable fleet combat

Players command ships with different sizes, movement characteristics, weapon capacities and battlefield roles. Fourteen weapons provide hitscan, homing, explosive, piercing and area-of-effect behaviours. Turret limits, line of sight, target priorities and fog-of-war visibility determine when a weapon can acquire and fire at a target.

Weapons and projectiles are assembled from reusable ECS components instead of separate logic for every weapon. The [targeting system](Assets/Scripts/Gameplay/Player/Weapons/Targeting/WeaponTargetingSystem.cs), [firing pipeline](Assets/Scripts/Gameplay/Player/Weapons/Firing/WeaponFiringSystem.cs) and [projectile processing](Assets/Scripts/Gameplay/Projectiles/ProjectileSystem.cs) can be combined with different authoring data to create new behaviours. Shader Graph and VFX Graph provide neon materials, trails, muzzle flashes and explosions without making presentation logic part of the combat simulation.

### Procedural encounters and campaign progression

Each run connects multiple encounters through a generated starmap. Nodes communicate their terrain, threat, enemy profile and rewards to the next level, giving players meaningful route choices before deployment.

The [starmap generator](Assets/Scripts/FleetCommandHub/Starmap/MapGenerator.cs) builds the route structure, while the [level-generation pipeline](Assets/Scripts/LevelGeneration/LevelGen_TerrainSystem.cs) creates terrain and prepares valid locations for the mothership, hives and other entities. During combat, hives apply constant pressure through trickle spawns and periodic surges. New hive-seed events and time-based difficulty scaling discourage passive play.

### Fleet customization and upgrades

Between encounters, players manage their roster in the hangar. Ships have class-specific systems, weapon mounts and capacity limits. Weapons and ship systems can be upgraded with resources earned during a run, while new blueprints expand the available fleet.

Data-driven [ship blueprints](Assets/Scripts/FleetCommandHub/Hangar/ShipBlueprintData.cs), [weapon definitions](Assets/Scripts/FleetCommandHub/Hangar/WeaponData.cs) and reusable UI elements allow the hangar to reflect the player's current inventory and loadout. The same data is handed to the gameplay scene so deployed entities match the selected configuration.

### Fog of war and tactical control

Fog of war limits both what the player sees and what weapons are allowed to target. Ships reveal different sensor areas, encouraging fleet positioning instead of allowing weapons to engage hidden enemies automatically. The [fog-of-war systems](Assets/Scripts/Gameplay/FogOfWar/FogOfWarSystem.cs) maintain visibility state, fade the visual layer and coordinate entity culling.

Players can box-select ships, add to a selection, split groups, issue formation-aware movement orders and choose the direction ships should face on arrival. The [fleet command system](Assets/Scripts/Input/Gameplay/FleetCommandSystem.cs) translates those commands into destinations that account for formation spacing and invalid terrain.

## Gameplay loop

1. Configure ships, weapons and upgrades in the hangar.
2. Choose an encounter on the starmap.
3. Deploy the fleet and destroy every enemy hive while protecting the mothership.
4. Collect biomass during combat to call in reinforcements.
5. Earn blueprints and upgrade cores, then prepare for the next level.

## Controls

| Action | Input |
| --- | --- |
| Move camera | `WASD`, move the cursor to a screen edge, or drag with the middle mouse button |
| Zoom | Mouse wheel |
| Select ships | Left-click and drag |
| Add to selection | Hold `Shift` while selecting |
| Move selected ships | Right-click |
| Set arrival direction | Hold and drag the right mouse button |

Ships navigate around terrain automatically. Equipped weapons acquire and fire on visible targets that meet their range and targeting rules.

## Technology

The runtime combines traditional Unity objects for menus and presentation with data-oriented ECS systems for simulation-heavy gameplay.

- Unity `6000.4.0f1`
- C#
- Unity DOTS: Entities, Job System and Burst Compiler
- Universal Render Pipeline
- Unity Input System
- UI Toolkit with UXML and USS
- Shader Graph and VFX Graph
- Blender and Inkscape for mesh and vector-asset workflows

## Run from source

### Requirements

- [Unity Hub](https://unity.com/download)
- Unity Editor `6000.4.0f1`
- Git

### Setup

```text
git clone https://github.com/TzeYuon/void-bastion.git
```

1. In Unity Hub, select **Add > Add project from disk** and choose the cloned directory.
2. Open the project with Unity `6000.4.0f1`.
3. Allow Unity to restore packages and import assets. The first import may take several minutes.
4. Open `Assets/Scenes/BootstrapScene.unity`.
5. Enter Play mode.

The repository intentionally excludes generated Unity folders such as `Library`, `Temp`, `Logs`, `Obj` and `UserSettings`.

## Team contributions

The project was designed, reviewed and playtested jointly. The main areas of ownership during development were:

| Contributor | Primary contributions |
| --- | --- |
| [Kean Lai](https://github.com/JamesLee5182) | Project and game-state foundations, player ships, swarm and spatial systems, weapons and projectiles, hangar and starmap UI, upgrades, selection controls, shields and gameplay polish |
| [Chan Tze Yuon](https://github.com/TzeYuon) | Terrain generation, grid utilities, flow-field and ship pathfinding, map variations, hive-seed events, difficulty work, audio and music, balancing and gameplay polish |
| Both | Game design, architecture discussions, testing, bug fixing, milestone documentation and release preparation |

Development took place from May to July 2026 through feature branches, issues and pull requests. The repository history preserves the progression from the first playable level to the complete multi-level run.

## Testing

The team used structured manual scenarios and repeated regression playtests to validate camera controls, fleet commands, combat, progression, terrain generation, enemy behaviour and state transitions. Deterministic grid and upgrade logic was also checked against known inputs during development. Automated Unity test assemblies are not included in this repository snapshot.

## Project context

NUS Orbital is an independent software-development programme. This project was completed at the **Apollo** level over three milestones:

- **Milestone 1:** a playable combat level with ships, weapons, enemies, terrain and win/loss states.
- **Milestone 2:** a complete run structure with a hangar, upgrades, rewards and level selection.
- **Milestone 3:** additional ships, weapons and enemies, fog of war, hive events, audio, balance and performance improvements.

## Credits and third-party software

The team created the game code, procedural systems, interface, vector art, meshes and original audio used by the project unless otherwise noted.

Third-party components include:

- [DOTween](http://dotween.demigiant.com/) by Demigiant
- [Roboto Mono](https://fonts.google.com/specimen/Roboto+Mono), distributed under the SIL Open Font License
- Google External Dependency Manager for Unity
- Unity packages distributed under their respective Unity licences

Third-party components and assets remain subject to their respective licences. Contact the maintainers before redistributing the complete project or its builds.
