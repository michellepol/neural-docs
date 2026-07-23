# UNavigationQueryFilter

UNavigationQueryFilter is primarily used to filter paths based on certain
criteria, such as agent properties (radius, height, etc.) or other custom
navigation rules. It is often used to filter out paths that may be obstructed
or unsuitable for certain types of agents, or to create more specialized
pathfinding logic. When using custom filters, it's essential to ensure
compatibility between the filter and the agent's navigation properties to avoid
issues in pathfinding.

UNavigationQueryFilter is a class used in Unreal Engine to define a
filter for navigation queries.

It allows:

- customization of pathfinding
queries,
- enabling more control over the conditions of movement and navigation
through the game world.

This class provides a way to filter out or include
certain types of paths or agents when performing navigation operations like
moving an AI pawn through the level.

It is commonly used in conjunction with UNavigationSystemV1, FPathFindingQuery,
and AINavigationData to control how pathfinding queries behave, especially when
different movement types, obstacles, or conditions need to be considered during
navigation.

## Usage Example

Basic Example of Creating a Custom Filter

```cpp
#include "NavFilters/NavigationQueryFilter.h"

// Create a new navigation query filter
UNavigationQueryFilter* MyFilter = NewObject<UNavigationQueryFilter>();

// Define which agent types are compatible with this filter
FNavAgentProperties AgentProperties;
AgentProperties.AgentRadius = 100.0f;
AgentProperties.AgentHeight = 180.0f;
MyFilter->SupportedAgentTypes.Add(AgentProperties);

// Apply the filter to a pathfinding query
FPathFindingQuery Query;
MyFilter->ApplyTo(Query);

// Now, use this query in pathfinding operations

Using the Filter with a Navigation System

// Assuming we have a valid navigation system and a target location
UNavigationSystemV1* NavSystem = UNavigationSystemV1::GetCurrent(GetWorld());
UNavigationQueryFilter* QueryFilter = NewObject<UNavigationQueryFilter>();

// Perform pathfinding with the custom filter
FNavLocation ResultLocation;
if (NavSystem->GetPathToLocation(TargetLocation, ResultLocation, QueryFilter))
{
    // Pathfinding succeeded with the custom filter
}
    void ACustomAIController::MoveToLocationWithCustomFilter(FVector TargetLocation)
    {
        // Create a custom navigation query filter
        UNavigationQueryFilter* CustomFilter = NewObject<UNavigationQueryFilter>();

        // Set agent-specific properties
        FNavAgentProperties AgentProperties;
        AgentProperties.AgentRadius = 50.0f;
        AgentProperties.AgentHeight = 100.0f;
        CustomFilter->SupportedAgentTypes.Add(AgentProperties);

        // Apply the filter and perform pathfinding
        FNavLocation ResultLocation;
        UNavigationSystemV1* NavSystem = UNavigationSystemV1::GetCurrent(GetWorld());
        if (NavSystem)
        {
            NavSystem->GetPathToLocation(TargetLocation, ResultLocation, CustomFilter);
        }
    }
```

## Navigation Filters in Unreal Engine

In Unreal Engine, Navigation Filters are used to control and customize
pathfinding behavior, allowing you to influence which paths are considered
valid for navigation based on certain criteria. These filters are used to
define how pathfinding queries are processed, enabling more sophisticated
control over AI movement. By applying navigation filters, you can adjust the
way agents navigate through the world depending on the environment, agent type,
and specific gameplay requirements. Key Concepts of Navigation Filters

    Pathfinding Query: A pathfinding query in Unreal Engine typically involves
    defining a start location, a destination location, and certain movement
    constraints. The navigation filter can influence how these queries are
    processed, filtering out invalid or undesirable paths.

    Navigation Query Filters: These filters provide a way to enforce specific
    movement rules when the AI or pawn is navigating across a navigation mesh.
    Filters can restrict certain types of movement (e.g., forbidding movement
    through water or certain terrain types) or adjust agent behavior (e.g.,
    making the agent move more cautiously in dangerous areas).

    Agent Types: Different agents (AI characters, vehicles, etc.) can have
    different navigation constraints. For example, a character might not be
    able to traverse an area where a large vehicle can pass. Filters help
    manage these constraints by associating specific agent types with custom
    navigation rules.

    Costing: Filters can apply different costs to the paths calculated by the
    navigation system. A path with a higher cost might be avoided by the AI,
    depending on how the cost is set in relation to other paths.

## How Navigation Filters Work in Unreal Engine

    Querying with a Filter: When an AI agent requests a path (e.g., using
    MoveToLocation), a pathfinding query is issued to the navigation system.
    The query can include a filter to specify the conditions under which the
    query should be evaluated.

    UNavigationQueryFilter Class: The class UNavigationQueryFilter is used to
    define the conditions and constraints applied to a pathfinding query. It
    works with FPathFindingQuery to filter paths based on factors like agent
    properties, obstacle considerations, and terrain types.

    Supported Agent Types: Filters can be tailored to specific agent types,
    such as NPCs, characters, or vehicles. For instance, you can define
    different filter settings for small agents that can navigate tight spaces
    and large agents that need broader paths. The supported agents are defined
    using FNavAgentProperties, which include properties like radius, height,
    and maximum slope.

    Custom Navigation Layers and Areas: Filters can also be used to create
    custom layers in the navigation mesh or assign specific traversal rules to
    different areas of the level. For example, a filter can be created to
    ensure that only specific agents can navigate across certain areas (e.g.,
    dangerous areas, water, or lava).

    NavMesh Filtering: You can set up filters to exclude or include specific
    types of terrain or obstacles. Filters can interact with the navigation
    mesh (NavMesh) to control which areas are valid for navigation based on
    predefined criteria. This helps in adjusting AI behavior in more complex
    environments.

## Common Use Cases for Navigation Filters

    Agent-Specific Navigation: Different agents (e.g., small NPCs vs. large
    vehicles) might need different navigation behavior. For instance, a small
    character can walk through a narrow corridor, but a larger vehicle should
    avoid it. By using filters, you can apply custom constraints that define
    which paths are appropriate for each agent type.

    Environment-Based Navigation: Filters can be used to create specific rules
    for certain terrain types. For example, you may want AI to avoid moving
        through water or steep slopes. Filters allow you to create rules to
        bypass these types of terrain.

    Restricted Areas: You can use filters to define restricted zones or
    dangerous areas that AI should avoid, like areas with enemies or traps.
    This helps ensure that AI will navigate around these zones without entering
    them.

    Dynamic Path Costing: Filters allow you to add dynamic path costing based
    on conditions like the presence of obstacles, damage zones, or
    environmental hazards. For example, a filter could assign a higher cost to
    paths that go through an area with fire, making the AI prefer safer routes.

    Gameplay-Specific Customization: Navigation filters can be used for
    gameplay reasons, such as forcing AI to avoid certain regions during
    stealth missions, preventing enemy AI from traversing specific pathways, or
    guiding characters along story-driven routes.

## Key Classes and Functions

    UNavigationQueryFilter: This class defines the filter used in navigation
    queries. Filters specify how paths should be evaluated and can impose
    restrictions on movement based on agent properties, area types, and other
    factors.

    FNavAgentProperties: A structure that defines the properties of an agent,
    such as the radius, height, and other characteristics that can influence
    how it navigates the world.

    FPathFindingQuery: This structure represents a pathfinding query, including
    the start location, end location, and any filters applied to the
    pathfinding request.

    UNavigationSystemV1: The class responsible for managing navigation data,
    including the creation of navigation meshes (NavMesh) and pathfinding
    queries. It handles all pathfinding operations and interacts with query
    filters.

## Best Practices

- Limit Complexity: Be mindful of the complexity of the filters you apply.
  Highly detailed filters or excessive checks might slow down pathfinding,
  especially in large environments with many agents.

- Test in Different Environments: When using navigation filters, test the paths
  in various game environments. Make sure that agents can still find valid
  paths while respecting your constraints.

- Combine with Dynamic Elements: Use filters in combination with dynamic
  elements in your game world, such as moving obstacles or changing
  environmental conditions (e.g., when doors open/close).

- Optimize Agent Properties: When defining agent properties (e.g., radius and
  height), ensure they align with the expected behavior in your level to avoid
  inconsistent results in pathfinding.

```
FPathFindingQuery Query(*GetPawn(), *NavSystem->GetDefaultNavData(), GetPawn()->GetActorLocation(), TargetLocation);
Query.QueryFilter = MyCustomFilter;  // Apply your custom filter

NavSystem->FindPathSync(Query);
```
