# UEBS 2 Animation Creation

## Purpose
Create and manage animations for Ultimate Epic Battle Simulator 2 (UEBS 2) using Blender and Unity. Handle large-scale unit animations, crowd simulation setups, and battle scene animation workflows.

## Context
- SummonAKit project for Blender + UEBS 2 animation
- Star Wars themed models available
- Pipeline: Blender (create/rig/animate) → FBX → Unity → UEBS 2

## When to Use
- Creating unit animations (idle, walk, run, attack, death)
- Batch processing animations for large armies
- Setting up animation state machines for UEBS 2 units
- Optimizing animations for thousands of simultaneous units
- Creating Star Wars themed battle animations

## UEBS 2 Animation Requirements

### Unit Animation Set (Minimum)
```
Required Animations per Unit:
├── Idle          — looping, 2-4 seconds
├── Walk          — looping, matched to movement speed
├── Run           — looping, matched to run speed
├── Attack_01     — single play, 0.5-1.5 seconds
├── Attack_02     — variation (optional)
├── Hit_React     — single play, 0.3-0.5 seconds
├── Death_01      — single play, 1-2 seconds
├── Death_02      — variation (optional)
└── Victory       — looping or single play
```

### Animation Specifications
- Frame Rate: 30 FPS (matches UEBS 2 playback)
- Root Motion: Optional, depends on unit type
- Bone Count: Keep under 30 for crowd units (performance)
- Animation Compression: Keyframe Reduction in Unity

## Blender Animation Workflow

### 1. Rig Setup for Crowd Units
- Use simplified rigs (15-25 bones max)
- No facial bones for distant crowd units
- IK chains for legs, FK for arms (combat flexibility)
- Single root bone at origin

### 2. Animation Naming Convention
```
{unit_name}_{action}_{variation}
Examples:
  stormtrooper_idle_01
  stormtrooper_walk_forward
  stormtrooper_attack_blaster_01
  stormtrooper_death_fall_back
  jedi_attack_saber_01
```

### 3. Batch Animation Script
```python
import bpy
import os

ANIMATIONS = {
    'idle': (1, 60),
    'walk': (61, 120),
    'run': (121, 180),
    'attack_01': (181, 210),
    'death_01': (211, 260),
}

def export_animations(armature_name, output_dir, unit_name):
    arm = bpy.data.objects[armature_name]
    for anim_name, (start, end) in ANIMATIONS.items():
        arm.animation_data.action = bpy.data.actions.get(
            f"{unit_name}_{anim_name}"
        )
        if arm.animation_data.action:
            filepath = os.path.join(
                output_dir, f"{unit_name}_{anim_name}.fbx"
            )
            bpy.context.scene.frame_start = start
            bpy.context.scene.frame_end = end
            bpy.ops.export_scene.fbx(
                filepath=filepath,
                use_selection=True,
                bake_anim=True,
                bake_anim_use_all_actions=False
            )
```

## Unity Animator Setup for UEBS 2

### State Machine Structure
```
Animator Controller: Unit_Controller
├── Parameters:
│   ├── Speed (float) — movement speed
│   ├── InCombat (bool) — engaged with enemy
│   ├── AttackIndex (int) — attack variation
│   ├── IsDead (bool) — unit eliminated
│   └── TakeDamage (trigger) — hit reaction
│
├── Base Layer:
│   ├── Idle → Walk (Speed > 0.1)
│   ├── Walk → Run (Speed > 0.5)
│   ├── Walk → Idle (Speed < 0.1)
│   ├── Any State → Attack (InCombat && AttackIndex > 0)
│   ├── Any State → Hit_React (TakeDamage)
│   └── Any State → Death (IsDead)
│
└── Death is final state (no exit transitions)
```

## Performance Optimization
- **GPU Instancing**: Enable on all unit materials
- **Animation Instancing**: Use for 1000+ identical units
- **LOD Animations**: Simplified anims at distance
  - Near (< 50m): Full animation, 30 FPS
  - Mid (50-150m): Reduced bones, 15 FPS
  - Far (> 150m): 2-frame blend or static pose
- **Crowd baking**: Pre-bake animation textures for massive groups
- **Culling**: Disable animators when off-screen

## Star Wars Unit Types
| Unit | Bone Count | Animations | Notes |
|------|-----------|------------|-------|
| Stormtrooper | 22 | Full set | Blaster + melee |
| Jedi/Sith | 25 | Full set + specials | Lightsaber, force push |
| Droid (B1) | 18 | Full set | Mechanical movement |
| Vehicle | 8 | Drive, fire, explode | Simplified rig |
| Creature | 20 | Full set | Organic movement |
