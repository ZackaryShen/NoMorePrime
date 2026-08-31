# No More Prime Tower — Research Index

Feishu doc (authoritative): https://snapmaker.feishu.cn/docx/O5hNdXSY7oTdIqx8QGuchffunWg

Topic: eliminating / minimizing the wipe (prime) tower for multi-color FDM printing
in Snapmaker Orca. Covers market landscape (slicer / firmware / hardware routes),
patent situation (Stratasys vs Bambu), local codebase readiness
(WipingExtrusions, Local-Z, Lightning infill, PARAMETER_MODIFIER), an adversarially
reviewed core scheme ("Internal Purge Reservoir", blockers included), and a 4-phase
implementation roadmap with verification gates.

Diagrams (hand-written SVG, rendered via Feishu whiteboard blocks):

- `diagrams/fig1_problem.svg` — why the tower exists, its three duties, its cost
- `diagrams/fig2_landscape.svg` — industry landscape: software / firmware / hardware routes
- `diagrams/fig3_scheme.svg` — core scheme: internal purge reservoir + tiered fallback
- `diagrams/fig4_roadmap.svg` — P0 audit → P1 decoupling → P2 MVP → P3 auto-planning

Key adversarial-review facts (all verified against source):

- Fill entities are generated before the wipe-tower planning step (posInfill <
  psWipeTower), so an absorption planner has access to all candidate entities.
- `flush_into_infill/objects/support` only work when the tower is enabled; the
  consume chain lives entirely inside `_make_wipe_tower` (Print.cpp:3383).
- The no-tower toolchange path (`set_extruder`, GCode.cpp:8437) emits no purge
  extrusions at all — this is the main gap.
- Single-layer absorption is off by an order of magnitude vs worst-case flush
  volume (108 mm³ available vs 800 mm³ needed) → cross-layer budgeting required.

SVG sources are plain SVG 1.1 (no filters/foreignObject) so Feishu whiteboard
renders them faithfully; PNG twins are generated for quick local review.
