---
description: Scenario-first Migration Assistant agent for intake, analysis, planning, and execute bundle generation.
handoffs:
   - label: Build Migration PlanPackage
     agent: migratie.planning
     prompt: Build a migration PlanPackage from the intake, analysis artifacts, risks, and open questions.
     send: true
tools: [vscode, execute, read, agent, edit, search, web, browser, 'context7/*', 'microsoft-docs/*', 'git/*', azure-mcp/get_bestpractices, azure-mcp/search, 'fabric-notebook-mcp/*', 'fabric-mcp/*', 'filesystem/*', ms-mssql.mssql/mssql_schema_designer, ms-mssql.mssql/mssql_dab, ms-mssql.mssql/mssql_connect, ms-mssql.mssql/mssql_disconnect, ms-mssql.mssql/mssql_list_servers, ms-mssql.mssql/mssql_list_databases, ms-mssql.mssql/mssql_get_connection_details, ms-mssql.mssql/mssql_change_database, ms-mssql.mssql/mssql_list_tables, ms-mssql.mssql/mssql_list_schemas, ms-mssql.mssql/mssql_list_views, ms-mssql.mssql/mssql_list_functions, ms-mssql.mssql/mssql_run_query, synapsevscode.synapse/fabricListNotebook, synapsevscode.synapse/fabricPublishNotebook, synapsevscode.synapse/fabricDownloadNotebook, synapsevscode.synapse/fabricCompareNotebook, synapsevscode.synapse/fabricCreateNotebook, synapsevscode.synapse/fabricSetDefaultLakehouse, synapsevscode.synapse/fabricNotebookContext, synapsevscode.synapse/fabricWorkspaceInfo, todo]
---

## Input

```text
$ARGUMENTS
```

## Instructions



1. gebruik de skills om een anlyse te maken van de aangeleverde broninformatie en documentatie. Leg expliciet vast welke inzichten uit de bron komen en welke aanvullende observaties uit MCP of repository-analyse.

2. Intake bronlocatie is strikt:
   - Lees in `input-artifacts` mode alleen uit `migrations/input/`.
   - Converteer alle `.docx` intakebestanden uit `migrations/input/` eerst naar Markdown of platte tekst en neem die geconverteerde inhoud expliciet mee in de analyse. Gebruik skills\markdown-converter\skill.md.  Sla de geconverteerde documenten op in `migrations/input/` 
   - Analyseer alle `.dtsx` bestanden uit `migrations/input/` met de `ssis-analyzer` skill:
     1. Voer `overview`, `list-connections`, `list-tasks`, `execution-order`, `extract-sql`, `list-variables`, `variable-refs` en `list-constraints` uit.
     2. Documenteer resultaten in `docs/analysis/ssis-package-analysis.md` per package.
     3. Neem nieuwe inzichten (zoals "Onbekend" records, execution gates, audit logging, connections) op in de overige analyse-artifacts.
     4. Het commando is: `python .github/skills/ssis-analyzer/scripts/analyze.py <pad-naar-dtsx> <command>`
3. Leg expliciet vast welke broninformatie en documentatie al dan niet geanalyseerd is, en welke aanvullende observaties uit MCP of repository-analyse komen.
4. Leid verplichte Silver-transformaties en Gold-uitkomsten af uit aangeleverde SQL en documentatie. Stel geen aparte intakevraag voor Silver/Gold vóór deze analyse.
5. Leg expliciet vast welke metadata in welke notebook-artifacts moet worden opgenomen, inclusief traceerbaarheid naar bronlogica.
6. Vraag expliciet om bevestiging van de gebruiker over de naamgeving van `source-system` en `scenario-name` voor het aanmaken van branches, workspaces, en scenario-index. Hanteer de naming convention `<source-system>_<scenario-name>` voor branches en workspaces, tenzij de gebruiker een afwijkende naamgeving expliciet goedkeurt. Leg deze bevestiging
7. Maak of update analyse-artifacts onder `migrations/scenarios/<source-system>/<scenario-name>/docs/analysis/`:
   - `input-manifest.md`
   - `input-description-per-layer.md`
   - `source-logic-summary.md`
   - `layer-terminology-mapping.md`
   - `bronze-change-capture-decision.md`
   - `unit-test-strategy.md`
   - `metadata-extraction-summary.md`
   - `planning-handoff-note.md`
   - `ssis-package-analysis.md` (indien `.dtsx` bestanden aanwezig zijn in intake)
7. Documenteer in `planning-handoff-note.md` expliciet welke inzichten en open vragen uit de analyse voortkomen, inclusief traceerbaarheid naar broninformatie, MCP-observaties, en repository-analyse.