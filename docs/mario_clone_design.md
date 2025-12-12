# "Skybound Plumber" – Detailed Mario Bros. Clone Design

This document outlines a richly featured, modernized clone of **Super Mario Bros.**, designed for desktop and web builds (Unity/Monogame/Godot-friendly) while preserving the crisp feel of the original games.

## Vision & Design Pillars
- **Authentic feel, modern reliability**: Pixel-perfect collision, responsive sub-frame buffering for inputs, 60 FPS target with deterministic physics replay support.
- **Discoverable depth**: Hidden blocks, branching routes, skill-based secrets (shell jumps, wall bounces), and optional challenge coins per level.
- **Playful rhythm**: Enemy spacing, coin arcs, and camera easing choreographed to soundtrack tempo; every 8 bars introduce a twist (new hazard, tempo change, or verticality shift).
- **Fair readability**: Clear silhouettes, parallax layers separated by value/contrast, and generous telegraphs for new hazards.

## Core Gameplay
### Player Moveset
- **Run/Walk**: Variable speed (walk 2.4 tiles/s, run 3.6 tiles/s), acceleration 12 tiles/s², deceleration 16 tiles/s².
- **Jump**: Coyote time 120 ms; jump buffer 80 ms. Base jump apex 4.2 tiles; variable height based on button hold up to 240 ms.
- **Bounce & Rebound**: Stomping an enemy grants a 20% higher rebound if jump is held; shell kicks inherit player momentum.
- **Wall Interactions**: Optional wall-slide (-40% gravity) and wall-jump (launch angle 70°, speed 4.0 tiles/s) for advanced stages.
- **Swimming**: Reduced gravity (35%) with timed strokes and buoyancy drag; bubble timers for pacing.

### Power-Ups
- **Super Mushroom**: +1 hit, taller collider, break brick blocks.
- **Fire Flower**: Ranged attacks; fireballs bounce twice, fizzle on lava; 0.35 s cooldown.
- **Sky Leaf (Tanooki analogue)**: Short glide with momentum carry; tail swipe reflecting small projectiles.
- **Star**: 8 s invincibility; tempo doubles, coin value +1 during effect.
- **Mini Shroom**: Halves collider, higher jump, water skitter; balanced with increased knockback.

### Items & Collectables
- **Coins** (arc-driven placement for jump teaching), **1-Ups**, **Time Orbs** (-5 s to timer), **Red Coins** (collect 8 for power-up), **Challenge Medals** (three per stage, hidden).
- **P-Switch**: Temporarily converts coins/blocks; timer syncs with music stinger.

### Enemies & Hazards
- **Goober (Goomba-like)**: Simple patrol, turns on edges; vulnerable to stomp/fire.
- **Sheller (Koopa-like)**: Withdraws into shell when stomped; shell velocity decays 10%/s, rebounds off walls.
- **Spiker**: Spiky top; immune to stomps, weak to fire or shell.
- **Hoverbat**: Sine-wave flyers; used for mid-air routes.
- **Lobber**: Throws arcing projectiles on cadence; player can bait throws.
- **Platform Hazards**: Falling lifts, disappearing blocks (on-beat), lava geysers with telegraphed steam.

### Level Structure
- **World Map**: Node-based overworld with optional secret exits; each world has 4–6 stages and a boss fortress.
- **Level Themes**: Grassland, Underground, Sky Islands, Volcano, Ice Caves, Factory, and Dreamscape (low gravity + palette shift).
- **Goals**: Flagpole-style standard exit, hidden warp doors, and time-attack exit (reach before stinger ends).
- **Checkpoints**: Midpoint tape; respawn retains power-up tier minus temporary buffs.

### Camera & Presentation
- **Camera**: Dead-zone framing with predictive lead based on velocity; clamps to level bounds; vertical snapping only when needed to reduce motion sickness.
- **UI**: Minimal HUD (score, coins, time, lives, power-up icon). World map uses diegetic signposts. Pause menu shows move list and completion stats.
- **Accessibility**: Colorblind-safe palettes, rumble intensity slider, remappable inputs, optional slow-motion training (80% speed with tinted overlay), and toggle for auto-run.

## Physics & Feel
- Deterministic fixed timestep (1/60s) with rollback-friendly input logs.
- Sub-pixel positioning with tile-size = 16 px; collision via swept AABB + surface normals to preserve momentum on slopes.
- Gravity tuned at 16 tiles/s²; terminal velocity clamped to 11 tiles/s.
- Input buffering across state transitions (e.g., exit pipe into buffered jump).
- Enemy AI uses simple state machines with designer-exposed params for speed, turn delay, and detection ranges.

## Level Design Patterns
- **Onboarding L1-1**: Teaches run/jump with coin arcs, first Goober on flat ground, first sheller near a pipe to demonstrate rebound.
- **Vertical Ascent**: Parallax clouds, disappearing blocks synchronized to percussion; optional wall-jump route rewards medal.
- **Water Stage**: Bubble corridors with hidden alcoves; lobster-like Lobbers teach projectile dodging underwater.
- **Fortress**: Auto-scrolling segments, crush blocks telegraphed by dust, boss door checkpoint.
- **Bosses**: Pattern-based, 3-hit structures with escalating tempo. Examples: Hoverbat King (swoop + sonar bursts), Factory Warden (conveyor belts + falling crates).

## Audio & Music
- Dynamic layering: add percussion during invincibility; muffle when underwater; stripped mix at low health.
- Sound priorities prevent spam (e.g., cap coin jingle concurrency). Duck music on dialogue/cutscenes.

## Technical Implementation
### Engine Architecture
- **Entity Component System**: Components for Transform, PhysicsBody, InputBuffer, Animation, Health, PowerUp, AIState, Collectable. Systems for Physics, Rendering, Audio, Interaction, Camera, UI.
- **Data-Driven Tuning**: JSON/ScriptableObject definitions for enemies, power-ups, and levels; live reloading in dev builds.
- **State Machines**: Player and bosses use hierarchical state machines (Idle → Run → Jump/Swim → Attack → Hurt → Death) with transition guards for buffered inputs.
- **Collision Layers**: Separate masks for terrain, semi-solid platforms, hazards, enemies, projectiles, and pickups.
- **Scripting Hooks**: Event bus for coins collected, power-up change, checkpoint reached, boss phase change; supports analytics and replays.

### Rendering
- Pixel-art friendly camera with integer scaling; letterbox/pillarbox when aspect ratio mismatches. Parallax layers at 0.2/0.5/0.8 depth factors. Sprite batching and texture atlases for performance.

### Tooling & Pipeline
- **Level Editor**: Grid-based with tile palette, enemy placement, test-play hotkey, and per-tile metadata (slippery, breakable, note blocks). Supports prefabs for common structures (stair stacks, pipe + piranha bundle).
- **Replay System**: Records input plus RNG seeds; can ghost playback for speedruns.
- **Localization**: String tables for HUD/menus; font atlas with accented characters.
- **Testing Harness**: Golden-run replays for regression, automated physics invariants (no tunneling, consistent jump heights), and lint for level validation (no softlocks, exit reachable, medal placements valid).

### Save/Progression
- Profile slots with world map completion, medal counts, best times, and unlocked challenge stages. Cloud-friendly JSON saves with checksum to prevent corruption.

## Content Roadmap
1. **Vertical Slice (World 1)**
   - 3 stages + fortress, Goober/Sheller enemies, Super + Fire power-ups, overworld navigation, checkpointing, basic HUD.
2. **Systems Expansion**
   - Water physics, P-Switch, Red Coins, replay system, level validation tooling.
3. **Feature Richness**
   - Sky Leaf, star invincibility music layering, additional worlds (Sky Islands, Factory), boss variations, accessibility polish.
4. **Post-Launch**
   - Time-attack seasonal events, daily challenge seeds, community level sharing.

## Visual Identity
- Bright primary palette for foreground; cooler desaturated backgrounds. Thick outlines for interactables. UI uses rounded pixel font; medals have unique silhouettes per world.

## Monetization & Platforms
- Single purchase or open-source with permissive assets; no ads. Target PC and WebGL; controller-first with keyboard fallback.

## Risks & Mitigations
- **Feel mismatch**: Use side-by-side physics benchmarks vs. reference. Record frame-by-frame comparisons.
- **Content creep**: Prioritize vertical slice, then expand via data-driven content.
- **Performance on Web**: Keep draw calls low; limit shader effects; optional 30 FPS fallback with adjusted physics scale.

## Sample Pseudo-Implementation (Godot-like)
```gdscript
# Player.gd (excerpt)
@export var run_speed := 230.0
@export var accel := 720.0
@export var deccel := 960.0
@export var gravity := 16 * 60.0
@export var jump_velocity := -620.0
@export var coyote_time := 0.12
@export var jump_buffer := 0.08

var coyote_timer := 0.0
var buffer_timer := 0.0
var velocity := Vector2.ZERO
var state := "idle"

func _physics_process(delta):
    # Input buffering
    if Input.is_action_just_pressed("jump"):
        buffer_timer = jump_buffer
    buffer_timer = max(buffer_timer - delta, 0)

    # Horizontal movement
    var target = (Input.get_action_strength("right") - Input.get_action_strength("left")) * run_speed
    if abs(target) > 0:
        velocity.x = move_toward(velocity.x, target, accel * delta)
    else:
        velocity.x = move_toward(velocity.x, 0, deccel * delta)

    # Gravity and coyote time
    if is_on_floor():
        coyote_timer = coyote_time
    else:
        coyote_timer = max(coyote_timer - delta, 0)
        velocity.y += gravity * delta

    # Jumping
    if buffer_timer > 0 and coyote_timer > 0:
        velocity.y = jump_velocity
        buffer_timer = 0
    if Input.is_action_just_released("jump") and velocity.y < 0:
        velocity.y *= 0.6  # variable height

    velocity = move_and_slide(velocity, Vector2.UP)
```

This design balances nostalgic familiarity with new exploration hooks, ensuring the clone feels both authentic and fresh.
