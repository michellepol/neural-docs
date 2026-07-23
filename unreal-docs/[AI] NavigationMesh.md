# NavigationMesh

A **NavMesh (Navigation Mesh)** in Unreal Engine is a system used to enable AI
characters (such as NPCs) to navigate the game world. It provides a way to
determine where AI agents can walk, where they cannot, and how they can move
from one location to another. The NavMesh system is built on a representation
of the navigable areas in the world and helps with pathfinding, movement, and
obstacle avoidance for AI-controlled characters.

## Key Concepts of NavMesh

1. **NavMesh Bounds Volume (NBV)**:
   - **NavMesh** is built based on the geometry and constraints defined within
     `NavMeshBoundsVolume`. This volume defines the region in the game world
     where the NavMesh is generated.
   - You typically place a `NavMeshBoundsVolume` around the area where you want
     to allow AI navigation. The size of the volume determines the area that
     will be covered by the NavMesh.

2. **Navigation Data**:
   - The `NavMesh` is stored as `NavData` in the world, and each level can have
     its own set of `NavMesh` data.
   - This data is generated based on the geometry of the level and the
     properties of the `NavMeshBoundsVolume`.

3. **NavMesh Generation**:
   - **NavMesh** generation occurs during the editor's **Build** process. Once
     you place a `NavMeshBoundsVolume` and ensure that walkable surfaces
     (static meshes, floors, etc.) are set up, you rebuild the navigation to
     generate the mesh.
   - The navigation mesh is dynamically created based on walkable surfaces and
     obstacles, providing a grid-like representation of the area where AI
     agents can move.

4. **Navigation Mesh Representation**:
   - The NavMesh is essentially a set of interconnected polygons, often
     triangles, that represent walkable areas.
   - Each triangle represents a "walkable" area that AI can navigate through,
     and if the AI controller or character attempts to walk, Unreal’s
     navigation system will calculate paths based on the nearest polygons.

## Creating and Setting Up NavMesh

1. **Add a NavMesh Bounds Volume**:
   - In the editor, go to the **Place Actors** panel and search for
     `NavMeshBoundsVolume`.
   - Place the volume in the areas where you want AI to be able to move.
   - You can resize and position the volume to match the areas where you want
     navigation to be enabled.

2. **Enable Navigation**:
   - To enable navigation in your game, go to the **World Settings** or
     **Project Settings**, and ensure that the **Navigation System** is
     enabled.
   - The `NavMesh` will be built when you rebuild the level, and you can see
     the navigation mesh in the editor by enabling the **Navigation** view mode
     (found in the viewport).

3. **Rebuilding the NavMesh**:
   - After setting up the NavMesh Bounds Volume, go to **Build** in the
     editor’s toolbar and click **Build Navigation**.
   - This will generate the navigation mesh based on your world’s geometry and
     the properties of the `NavMeshBoundsVolume`.

4. **Visualizing the NavMesh**:
   - In the editor viewport, you can enable **Navigation** visualization by
     going to the top bar of the editor and selecting the **View Mode**
     dropdown, then choosing **Navigation**.
   - This will show the generated NavMesh in the editor as a series of green
     (or different colored) polygons representing the walkable areas.

## Navigation and AI Pathfinding

Once the NavMesh is created, AI characters can use the navigation system to
find paths from one point to another.

- **AI Pathfinding**: To make an AI character move from one location to
  another, you can use pathfinding provided by the `UNavigationSystemV1` class.
  This will use the NavMesh to determine the best path.

```cpp
#include "NavigationSystem.h"
#include "AIController.h"
#include "GameFramework/Actor.h"
#include "Engine/World.h"

void AMyAIController::MoveToTarget(AActor* Target)
{
    if (Target)
    {
        UNavigationSystemV1* NavSystem = UNavigationSystemV1::GetCurrent(GetWorld());
        if (NavSystem)
        {
            FNavLocation TargetLocation;
            if (NavSystem->GetRandomPointInNavigableRadius(Target->GetActorLocation(), 1000.0f, TargetLocation))
            {
                MoveToLocation(TargetLocation.Location);
            }
        }
    }
}
```

This example shows how to get a valid navigation location close to a target and
move an AI-controlled character there.

## Modifying NavMesh Properties

There are various settings that allow you to customize the behavior of the
NavMesh and the AI's interaction with it. These settings are found in the
**Navigation System**.

1. **NavMesh Cost and Agent Radius**:
   - **Agent Radius**: Defines the size of the AI agent, which can affect the
     size of obstacles that are considered impassable.
   - **Agent Height**: Controls the height of the agent, which can affect
     whether the AI can walk under low obstacles.

2. **NavMesh Obstacles**:
   - By default, static meshes and other objects can block or limit movement on
     the NavMesh. You can set objects as navigation **obstacles** (for
     instance, furniture that blocks AI movement) or **walkable**.

3. **NavModifiers and NavAreas**:
   - You can use `NavModifierVolumes` or define custom **NavAreas** to change
     the properties of specific areas in the NavMesh (for example, making
     certain areas more difficult to navigate).

4. **Dynamic Navigation (Rebuilding NavMesh)**:
   - Unreal Engine allows for **dynamic navmesh rebuilding** in the runtime.
     For instance, when obstacles move or are destroyed, you can update the
     NavMesh during gameplay using
     `UNavigationSystemV1::UpdateNavMeshForBoundsVolume()` or similar
     functions.

## Types of Navigation in Unreal Engine

1. **Static Navigation**:
   - This is the most common type, where the NavMesh is precomputed and fixed
     during the level build process. It works well for static or mostly-static
     environments.

2. **Dynamic Navigation**:
   - Dynamic navigation is more flexible, allowing the NavMesh to update in
     real-time to account for changes in the environment (e.g., doors opening
     or objects being moved).
   - It can be more expensive in terms of performance because the NavMesh needs
     to be recalculated dynamically.

3. **NavMesh on Moving Platforms**:
   - For AI that needs to navigate on moving platforms, Unreal Engine supports
     **NavMesh on dynamic surfaces**. For instance, you can create a platform
     that moves, and the AI will automatically adjust to navigate on that
     platform.

## Common Navigation Use Cases

- **AI Pathfinding**: The NavMesh allows AI characters to move intelligently
  around obstacles, find paths to targets, and avoid collisions.
- **Character Movement**: Any `APawn` or `ACharacter` that needs to use
  AI-driven movement will rely on the NavMesh for determining movement paths.
- **Exploration AI**: NPCs or creatures can roam the world using the NavMesh to
  avoid obstacles, explore, and move to different locations.
