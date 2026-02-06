# Unity C# Scripting

## Purpose
Generate, modify, and debug C# scripts for Unity 2018.4 LTS projects. Handles MonoBehaviour lifecycle, component architecture, editor scripting, and Unity-specific patterns.

## Context
- Unity Version: 2018.4.16f1 (LTS)
- Scripting Runtime: .NET 4.x Equivalent
- API Compatibility: .NET Standard 2.0
- Project: 3D project with imported Star Wars models

## When to Use
- Creating new MonoBehaviour scripts
- Adding components, event handlers, or coroutines
- Writing editor scripts or custom inspectors
- Debugging null references, serialization issues
- Setting up scene logic, triggers, colliders

## Conventions
- Scripts go in `Assets/Scripts/` (create if missing)
- Editor scripts go in `Assets/Editor/`
- Use `[SerializeField]` for private inspector fields
- Use `[RequireComponent]` where dependencies exist
- Follow Unity 2018.4 API (no 2019+ features like `Awaitable`)
- Prefer coroutines over async/await for Unity 2018.4

## Templates

### MonoBehaviour
```csharp
using UnityEngine;

public class {{Name}} : MonoBehaviour
{
    void Start() { }
    void Update() { }
}
```

### ScriptableObject
```csharp
using UnityEngine;

[CreateAssetMenu(fileName = "New{{Name}}", menuName = "{{Category}}/{{Name}}")]
public class {{Name}} : ScriptableObject
{
    // Data fields here
}
```

### Editor Script
```csharp
using UnityEngine;
using UnityEditor;

[CustomEditor(typeof({{TargetType}}))]
public class {{Name}}Editor : Editor
{
    public override void OnInspectorGUI()
    {
        base.OnInspectorGUI();
    }
}
```

## Key Unity 2018.4 APIs
- Physics: `Raycast`, `OverlapSphere`, `BoxCast`
- Animation: `Animator`, `AnimationClip`, `AnimatorController`
- UI: `Canvas`, `EventSystem`, `UnityEngine.UI`
- Input: `Input.GetAxis`, `Input.GetButtonDown` (old input system only)
- Scenes: `SceneManager.LoadScene`, `SceneManager.LoadSceneAsync`

## Constraints
- No `Addressables` (not in 2018.4 by default)
- No `Universal Render Pipeline` unless manually added
- No `Input System` package (use legacy `Input` class)
- No `async/await` patterns with Unity lifecycle
- TextMeshPro 1.4.1 available via packages
