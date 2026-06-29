# concrete_block_assembly_interfaces

ROS 2 service definitions for wall-assembly *task sequencing* — the contract between the wall plan server in [concrete_block_assembly_planning](../concrete_block_assembly_planning/) (server) and [concrete_block_behavior_tree](../concrete_block_behavior_tree/) (client). These describe *what to build next*, not *how to move* (motion is [concrete_block_motion_planning](../concrete_block_motion_planning/)) and not *where blocks are* (state is [concrete_block_world_model_interfaces](../concrete_block_world_model_interfaces/)).

## Responsibilities

- Define the "give me the next block to place" request/response the BT loop polls each cycle.
- Define the "freeze the interactively-placed wall origin into the plan" request used during wall setup.

## Contents

| Kind | Name | Purpose |
|---|---|---|
| `srv` | `GetNextAssemblyTask` | Next block to place: ids, pickup/approach/target/reference poses, `has_task` flag (false ⇒ plan complete) |
| `srv` | `ConfirmWallOrigin` | Bake an interactively chosen wall origin (x, y, yaw + ground z) into a named plan; optionally persist and report reach/collision validity |

## Dependencies & interactions

Leaf package — depends only on `geometry_msgs`, `builtin_interfaces`; no other stack package.

```
behavior_tree ──GetNextAssemblyTask──► wall_plan_server   (assembly_planning)
rviz_plugins / RViz ──ConfirmWallOrigin──► wall_setup_node (assembly_planning)
```

- **Server:** [concrete_block_assembly_planning](../concrete_block_assembly_planning/) — `wall_plan_server` serves `GetNextAssemblyTask`; `wall_setup_node` serves `ConfirmWallOrigin`.
- **Client:** [concrete_block_behavior_tree](../concrete_block_behavior_tree/)'s `GetNextAssemblyTask` BT node drives the assembly loop in `wall_assembly.xml`.

## Build

```bash
colcon build --packages-select concrete_block_assembly_interfaces
```
