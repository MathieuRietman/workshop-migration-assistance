# Fabric Git Branching and Local CI/CD Strategy for Migration Factory

## Purpose

This strategy standardizes how migration teams work with GitHub, Microsoft Fabric workspaces, and local CI/CD during scenario-based migrations.

It is based on Microsoft Learn guidance for:
- Manage branches in Microsoft Fabric workspaces
- Local deployment with fabric-cicd

## Core Principles

1. Isolation first: every developer works in an isolated branch and isolated Fabric workspace.
2. Shared workspaces are integration targets, not development sandboxes.
3. No direct edits in shared Dev/Test/Prod workspaces.
4. Bronze, Silver, and Gold are delivered and validated as separate layers.
5. Every PR must include migration evidence and reconciliation evidence.

## Repository and Branch Model

### Long-lived branches

- `main`: production-ready artifacts only.
- `dev`: integration baseline for active migration work.
- `release/*`: optional stabilization branches when needed.

### Short-lived feature branches

For every scenario migration, create branch names with this format:

- `feature/mig/<source-system>/<scenario-name>/<developer-alias>`

Examples:
- `feature/mig/woz/sp-koophuur/anna`
- `feature/mig/brp/bewoning-correcties/jasper`

## Workspace Strategy in Fabric

### Shared workspaces

- `fab-dev-shared` connected to `dev`
- `fab-test-shared` connected to `release/*` or `main` based on release flow
- `fab-prod-shared` connected to `main`


- `fab-dev-ruimte-wonen` connected to `dev`
- `fab-test-ruimte-wonen` connected to `release/*` or `main` based on release flow
- `fab-prod-ruimte-wonen` connected to `main`

### Personal workspaces

Each developer gets one workspace per active scenario:

- `fab-dev-<alias>-<scenario>`

Examples:
- `fab-dev-anna-koophuur`
- `fab-dev-jasper-woz-vbo-koppeling`

### Branch out policy

1. Start from Source control in Fabric and use Branch out to another workspace.
2. Create a new branch and new workspace together whenever possible.
3. Commit all pending workspace changes before branch switching or branch-out.
4. Do not connect to an existing workspace unless deletion impact is reviewed.

## Folder and Artifact Standards

Scenario artifacts stay in:

- `migrations/scenarios/<source-system>/<scenario-name>/`

Minimum structure:

- `intake/` → maps to `analysis/` in scenario implementations
- `plan/` → maps to `planning/` in scenario implementations
- `execute/` → maps to `dacric_workspace/` in scenario implementations
- `validation/` → maps to `validation/` in scenario implementations
- `evidence/` → embedded in `validation/` + PR attachments

Layer-specific execute structure:

- `execute/notebooks/bronze/` → `dacric_workspace/notebooks/bronze_to_silver/`
- `execute/notebooks/silver/` → `dacric_workspace/notebooks/silver_to_gold/`
- `execute/notebooks/gold/` → `dacric_workspace/notebooks/schema/`
- `execute/fabric/` → `dacric_workspace/lakehouses/` + orchestration notebooks
- `execute/warehouse/` → `dacric_workspace/warehouse/` (when procedure-first)

## Local CI/CD Strategy with fabric-cicd

Use local deployment checks before PR merge.

### Local prerequisites

- Python 3.9 to 3.12
- Azure CLI login
- fabric-cicd installed
- Developer Fabric workspace id configured

### Local validation flow

1. Sync latest feature branch locally.
2. Run local publish to personal workspace.
3. Validate created or updated Fabric items.
4. Run layer tests in order: Bronze, Silver, Gold.
5. Export logs and attach evidence to PR.

### Suggested command sequence

1. `pip install fabric-cicd`
2. `az login --allow-no-subscriptions`
3. run local deploy script for scoped items
4. run validation scripts and reconciliation checks

## Pull Request Strategy

### Required PR checks

1. Branch policy: only feature branch to `dev`, never direct to `main`.
2. Artifact completeness:
   - intake updated
   - plan updated
   - execute artifacts generated
   - validation and evidence attached
3. Layer gate evidence:
   - Bronze pass
   - Silver pass
   - Gold pass
4. Reconciliation evidence against source logic.
5. Reviewer approval from migration governance owner.

### PR template sections

1. Scenario and source scope
2. Bronze changes
3. Silver changes
4. Gold changes
5. Test evidence
6. Reconciliation summary
7. Risks and rollback

## Agent Responsibilities

### migratie.assistant

- Enforces intake mode and branch/workspace naming at scenario start.
- Creates or updates strategy-aware intake artifacts.

### migratie.planning

- Produces PlanPackage with explicit branch and workspace targets.
- Includes layer gates and local CI/CD checkpoints.

### migratie.implementation

- Generates layer-separated implementation tasks and evidence map.
- Includes local publish and test tasks before PR.

### migratie.validation-governance

- Verifies branch policy, workspace isolation, and evidence gates.
- Blocks recommendation if mandatory Git/Fabric controls fail.

## Skill Matrix for Migration Factory

### Core skills to use

- `data-engineering-data-pipeline`
- `spark-engineer`
- `schema-exploration`
- `sql-optimization-patterns`
- `database-schema-designer`
- `senior-data-engineer`

### Skills to specialize for this migration domain

- `data-engineering-data-pipeline`: add Fabric medallion, OneLake naming, layer-gate tests.
- `spark-engineer`: add temporal parity and Silver-to-Gold conformance checks.
- `database-schema-designer`: add GGM-to-Kimball mapping guidance.

### Skills currently low value for this migration stream

- `geospatial-data-pipeline` (unless explicit geospatial scope exists)
- `dlt-skill` (unless dlt is selected as ingestion runtime)
- `jupyter-notebook` (only use when notebook scaffolding is requested)

## Skill Backlog (Create or Update)

1. Create `fabric-medallion-migration` skill.
2. Create `temporal-reconciliation` skill.
3. Create `ggm-to-kimball-mapping` skill.
4. Update existing core skills with migration-specific triggers and output templates.
5. Tag low-value skills as optional in migration agent routing.

## Governance Gates

1. Gate A: Intake quality and scope completeness.
2. Gate B: Plan quality and dependency order.
3. Gate C: Implementation completeness per layer (incl. SQL-first and orchestration-only).
4. Gate D: Metadata-driven controls (audit columns, idempotency, lineage).
5. Gate E: Local CI/CD deployment evidence (fabric-cicd publish + layer tests).
6. Gate F: Reconciliation and historical correctness.
7. Gate G: Privacy and access controls (column masking, RLS, BSN protection).
8. Gate H: Release recommendation (overall go / conditional-go / no-go).

Scenarios may add **sub-gates** (e.g., G.1 Branch isolation) but must map back to A-H.
The gate-overview template reflects all 8 gates.

## Folder Convention Mapping

The governance folder convention maps to the implementation target folder (`dacric_workspace/`) as follows:

| Governance Convention | Implementation Folder | Toelichting |
|---|---|---|
| `intake/` | `analysis/` | Intake analysis artifacts (input-manifest, layer descriptions, logic summary) |
| `plan/` | `planning/` | PlanPackage + gate-overview |
| `execute/notebooks/bronze/` | `dacric_workspace/notebooks/bronze_to_silver/` | Per-entity Bronze→Silver transformation notebooks |
| `execute/notebooks/silver/` | `dacric_workspace/notebooks/silver_to_gold/` | Per-entity Silver→Gold transformation notebooks |
| `execute/notebooks/gold/` | `dacric_workspace/notebooks/schema/` | Schema DDL notebooks per layer |
| `execute/fabric/` | `dacric_workspace/lakehouses/` + `dacric_workspace/notebooks/run_*.Notebook/` | Lakehouse definitions + orchestration notebooks |
| `execute/warehouse/` | `dacric_workspace/warehouse/` | SQL procedures (only when procedure-first decision) |
| `validation/` | `validation/test-results/` | Test evidence per gate |
| `evidence/` | `validation/test-results/` + PR attachments | Reconciliation evidence for PR |

### Warehouse Decision (SP-First)

When source contains stored procedures, the PlanPackage must include a formal **warehouse decision**:
- **Procedure-first**: Gold logic lives in `dacric_workspace/warehouse/` as `CREATE OR ALTER PROCEDURE`. Notebooks only orchestrate.
- **Notebook-first**: Gold logic lives in Spark SQL notebook cells. No `warehouse/` folder.
- Decision must be documented with motivation and rejected alternative.

## References

- https://learn.microsoft.com/en-us/fabric/cicd/git-integration/manage-branches?tabs=azure-devops
- https://learn.microsoft.com/en-us/fabric/cicd/tutorial-fabric-cicd-local
