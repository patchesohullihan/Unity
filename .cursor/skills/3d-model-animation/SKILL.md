# 3D Model & Animation

## Purpose
Handle 3D model import, setup, optimization, and animation within Unity. Covers OBJ/FBX/MB file workflows, material assignment, LOD setup, and animation configuration.

## Context
- Project contains Star Wars .obj and .mb (Maya Binary) models in `Assets/`
- Unity 2018.4 model import pipeline
- Standard Render Pipeline (Built-in)

## When to Use
- Importing or configuring 3D models (.obj, .fbx, .mb)
- Setting up materials and shaders for imported models
- Configuring model import settings (scale, normals, tangents)
- Creating or editing animation clips
- Setting up Animator Controllers and state machines
- LOD group configuration
- Mesh optimization (polygon reduction, batching)

## Model Import Checklist
1. Place model files in `Assets/Models/` (organize by category)
2. Select model in Project window → Inspector:
   - **Model tab**: Scale Factor, Mesh Compression, Read/Write Enabled
   - **Rig tab**: Animation Type (Generic/Humanoid/Legacy)
   - **Animation tab**: Import Animation, clip ranges
   - **Materials tab**: Material Naming, Search
3. Create Materials folder: `Assets/Materials/`
4. Extract or assign materials

## OBJ Import Notes
- OBJ files don't carry animation data
- Materials come from companion .mtl files
- Scale may need adjustment (OBJ has no standard unit)
- UV mapping preserved on import

## Maya Binary (.mb) Notes
- Requires FBX conversion for best results in Unity
- Maya references may not resolve — use FBX export from Maya
- Blendshapes and skinning transfer via FBX
- Recommend exporting from Maya as FBX 2018.1+

## Animation Setup
```
Animator Controller Structure:
├── Base Layer
│   ├── Idle (default state)
│   ├── Walk
│   ├── Run
│   └── Action
├── Parameters
│   ├── Speed (float)
│   ├── IsGrounded (bool)
│   └── ActionTrigger (trigger)
└── Transitions (with conditions)
```

## Shader Reference (Built-in Pipeline)
- `Standard` — PBR metallic workflow
- `Standard (Specular setup)` — PBR specular workflow
- `Unlit/Texture` — No lighting
- `Mobile/Diffuse` — Lightweight mobile shader

## Optimization Guidelines
- Keep draw calls under 2000 for desktop
- Use static batching for non-moving objects
- Use GPU instancing for repeated meshes
- Compress textures: Normal maps → BC5, Albedo → BC7/DXT5
- Mark non-deforming meshes as non-readable
