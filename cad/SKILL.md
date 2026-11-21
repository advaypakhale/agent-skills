---
name: cad
description: Build modular, parametric CadQuery models in Python and reverse-engineer STEP files into feature trees using parallel subagents and geometric verification. Use when creating, refactoring, or reverse-engineering 3D CAD parts and assemblies.
---

# CAD (CadQuery)

Build CAD models in Python with CadQuery (`cadquery as cq`) as modular, pure functions organized around engineering intent.

## Code structure

Organize models across modules by role:

- `lib/` or `hardware/` — reusable standard components (fasteners, standoffs, bearings, extrusions, mounts)
- `parts/` or `components/` — project-specific part and feature functions
- `assemblies/` or top-level entrypoint — global parameter blocks, part composition, and STEP/STL exports

Each component is a pure function that takes dimensions and returns a `cq.Workplane` or `cq.Assembly`. Keep file I/O and exports in top-level entrypoints.

## Parameterization

Define driving dimensions in a parameter block at the top of each part or assembly file using a frozen `dataclass` or grouped module constants.

```python
from dataclasses import dataclass
import cadquery as cq


@dataclass(frozen=True)
class BracketParams:
    width: float = 40.0
    thickness: float = 4.0
    hole_dia: float = 5.2
    hole_spacing: float = 28.0


def mounting_plate(p: BracketParams) -> cq.Workplane:
    return (
        cq.Workplane("XY")
        .box(p.width, p.width, p.thickness)
        .faces(">Z")
        .workplane()
        .rarray(p.hole_spacing, p.hole_spacing, 2, 2)
        .hole(p.hole_dia)
    )
```

### What to parameterize

Expose dimensions that a human designer would intentionally adjust and whose geometric outcome is predictable: envelope dimensions, mounting patterns, clearances, wall thicknesses, and mating interfaces.

Keep fixed or derived geometry local to the function:

- Separate reusable library parts from bespoke project geometry. Library components expose standard catalog dimensions; project-specific helpers only expose parameters that vary across the design.
- Compute or keep constant internal construction details whose effect cannot be reasoned about in isolation, such as blend radii tied to local topology, internal rib ratios, or fixed draft angles.
- If changing a value would break topological selectors or produce invalid geometry without a clear physical meaning, derive it from the primary parameters inside the function.

## Reverse-engineering STEP files

When converting a STEP file into CadQuery, recover the underlying design tree — base stock, primary extrusions and revolves, subtractive features, patterns, and dress-up fillets or chamfers — so the output reads like a native parametric model.

### 1. Inspect and decompose (Orchestrator)

Load the STEP file (`cq.importers.importStep`) and analyze its geometry and topology before writing model code:

- Measure bounding boxes, volume, surface area, center of mass, principal axes, and symmetry planes.
- Classify faces and edges (planes, cylinders, cones, tori, freeform surfaces) to locate datum planes, hole patterns, bosses, pockets, and fillets.
- Decompose the solid or assembly into a hierarchy of sub-components and feature groups that mirror a CAD feature tree.

### 2. Fan out to subagents

For non-trivial parts or multi-body STEP files, act as an orchestrator and spawn parallel subagents (`Agent` tool):

- Give each subagent a bounded sub-component, cross-section, or feature group along with its reference datum, bounding region, and interface dimensions.
- Have each subagent measure its assigned features from the STEP geometry and write a pure, parameterized CadQuery function for that component.
- Assemble the returned functions into the module hierarchy and wire shared driving parameters at the top level.

### 3. Verify against the STEP reference

Run an iterative verification loop comparing the generated CadQuery solid (`generated`) against the reference STEP solid (`reference`):

- **Mass and envelope properties**: compare bounding box extents, volume, surface area, and center of mass.
- **Volumetric boolean diff**: compute `reference.cut(generated)` (missing volume) and `generated.cut(reference)` (excess volume) to localize geometric errors.
- **Targeted refinement**: when a region diverges, dispatch a subagent with the bounding box of the diff volume to correct the corresponding feature function.

Stop refining once the primary geometry, mating interfaces, and feature tree match the reference within engineering tolerances. Prioritize readable, maintainable parametric code over chasing negligible fillet, spline, or floating-point discrepancies.
