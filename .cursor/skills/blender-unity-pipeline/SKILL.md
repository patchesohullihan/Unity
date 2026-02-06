# Blender-Unity Pipeline

## Purpose
Manage the asset pipeline between Blender and Unity. Handle model export/import, animation transfer, material conversion, and Python scripting for Blender automation via `bpy` and `blenderproc`.

## Context
- Dependencies: `bpy`, `blenderproc`, `requests` (from requirements.txt)
- Target: Unity 2018.4 with Standard Render Pipeline
- Source models: Star Wars assets (.obj, .mb)
- MCP SDK integration available (package.json)

## When to Use
- Exporting models from Blender to Unity
- Writing Blender Python scripts for batch processing
- Converting materials between Blender and Unity
- Automating model preparation (retopology, UV unwrap)
- Setting up BlenderProc for synthetic data or rendering
- Rigging characters in Blender for Unity import

## Export Settings (Blender → Unity)
```
FBX Export Settings:
├── Scale: 1.0 (Unity unit = 1 meter)
├── Apply Scalings: FBX All
├── Forward: -Z Forward
├── Up: Y Up
├── Apply Unit: checked
├── Apply Transform: checked
├── Armature:
│   ├── Primary Bone Axis: Y
│   └── Secondary Bone Axis: X
├── Animation:
│   ├── Baked Animation: checked
│   ├── NLA Strips: unchecked
│   └── Force Start/End Keying: checked
└── Mesh:
    ├── Smoothing: Face
    └── Tangent Space: checked
```

## Blender Python (bpy) Patterns

### Batch Export Script
```python
import bpy
import os

def export_all_fbx(output_dir):
    for obj in bpy.data.objects:
        if obj.type == 'MESH':
            bpy.ops.object.select_all(action='DESELECT')
            obj.select_set(True)
            bpy.context.view_layer.objects.active = obj
            filepath = os.path.join(output_dir, f"{obj.name}.fbx")
            bpy.ops.export_scene.fbx(
                filepath=filepath,
                use_selection=True,
                apply_scale_options='FBX_SCALE_ALL'
            )
```

### Animation Bake
```python
import bpy

def bake_animation(armature_name, start, end):
    arm = bpy.data.objects[armature_name]
    bpy.context.view_layer.objects.active = arm
    bpy.ops.nla.bake(
        frame_start=start,
        frame_end=end,
        only_selected=False,
        visual_keying=True,
        clear_constraints=True,
        bake_types={'POSE'}
    )
```

## BlenderProc Usage
```python
import blenderproc as bproc

bproc.init()
obj = bproc.loader.load_obj("path/to/model.obj")
# Set up lighting, camera, materials
bproc.renderer.enable_normals_output()
data = bproc.renderer.render()
```

## Material Conversion (Blender Principled → Unity Standard)
| Blender Principled BSDF | Unity Standard |
|--------------------------|----------------|
| Base Color | Albedo |
| Metallic | Metallic |
| Roughness | 1 - Smoothness |
| Normal Map | Normal Map |
| Emission | Emission |

## Common Issues
- **Scale mismatch**: Blender defaults to meters, verify FBX scale options
- **Rotation offset**: Use -Z Forward, Y Up in FBX export
- **Missing textures**: Pack textures before export or copy alongside FBX
- **Bone orientation**: Unity expects Y-up bones, Blender may use Z-up
- **Shape keys → Blend shapes**: Export with "Shape Keys" checked in FBX
