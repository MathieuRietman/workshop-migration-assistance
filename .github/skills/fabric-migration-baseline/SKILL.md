---
name: fabric-migration-baseline
description: "Baseline kennis voor migratie van on-prem and Synapse naar Microsoft Fabric zonder afhankelijkheid van input/ en specs mappen in de repository."
metadata:
	owner: migration-helper
	added: "2026-03-16"
---

# Fabric Migration Baseline

Gebruik deze skill als vaste kennisbron voor planning en implementatie wanneer bronbestanden of plandocumenten niet in de repository worden opgenomen.

## Use this skill when

- Je een migratiescenario naar Fabric plant of implementeert
- Je laagmapping en ingestiestrategie nodig hebt zonder `input/` of `specs/` als afhankelijkheid
- Je CDC or CDF or Mirroring keuze moet onderbouwen

## Baseline Knowledge

### 1. Layer mapping
- Datalanding/Staging -> Bronze
- PSA -> Bronze historisch
- Datastore -> Silver
- DWH -> Gold
- Datamarts and Datadelivery -> Gold marts and Delivery zone

### 2. Ingestion voorkeur bij gelijke functionaliteit
1. Mirroring
2. SQL 2025 Change Data Feed
3. CDC
4. Overige opties: Copy Job, Pipelines, Shortcuts, Eventstream or Eventhouse

### 3. SQL-first grenzen
- Businessregels in SQL or Spark SQL objecten
- Pipelines voor orchestratie, triggering, logging, foutafhandeling

### 3b. Jupyter notebook format voor source notebooks
- Source notebooks zijn `.ipynb` bestanden in `result/notebooks/` subfolders (editeerbaar)
- Jupyter notebook JSON format met Markdown cellen (documentatie) en Code cellen (Python/PySpark)
- Gebruik `%run` magic command in orchestratie notebooks
- Na deployment naar Fabric worden artifacts gesynchroniseerd naar `fabric_workspace/` in Fabric Git Integration format
- Spark Python setup: `import sempy.fabric as fabric`, `mssparkutils.lakehouse.get()`, `abfss://` paden
- Delta: `.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable()`
- Schema DDL: `CREATE TABLE IF NOT EXISTS ... USING DELTA`

### 3c. Metadata en documentatie preservatie (verplicht)
- **SQL comments**: Neem inline comments en header comments uit bron-SQL bestanden over in notebook Markdown cellen
- **Business rules**: Extraheer en documenteer business rules uit stored procedures in Markdown cellen boven relevante code
- **Transformatielogica**: Leg complexe transformaties uit met comments in code en Markdown secties
- **Column documentatie**: Neem kolombeschrijvingen uit bronschema's over in schema DDL comments
- **Edge cases**: Documenteer edge cases, validatielogica en data quality checks expliciet
- **Bronverwijzingen**: Verwijs in notebooks naar originele bron-objecten (tabel/view/SP namen) voor traceerbaarheid
- **Verduidelijking**: Voeg waar nodig extra uitleg toe voor complexe logica die niet direct duidelijk is

### 4. Verplichte scenario artifacts
- `docs/analysis/input-manifest.md`
- `docs/analysis/input-description-per-layer.md`
- `docs/analysis/source-logic-summary.md`
- `docs/analysis/layer-terminology-mapping.md`
- `docs/analysis/bronze-change-capture-decision.md`
- `docs/analysis/unit-test-strategy.md`
- `docs/analysis/metadata-extraction-summary.md` (SQL comments, business rules, documentatie uit intake)
- `docs/planning/plan-package.md`
- `docs/documentation/implementation-package-overview.md`
- `docs/validation/governance-report.md`
- `docs/validation/test-results.md`
- `docs/index.md` (central navigation hub)
- `result/notebooks/bronze_to_silver/` (.ipynb bestanden per entity)
- `result/notebooks/silver_to_gold/` (.ipynb bestanden per entity)
- `result/notebooks/schema/` (schema DDL notebooks)
- `result/notebooks/run_bronze_to_silver.ipynb`, `run_silver_to_gold.ipynb` (orchestratie)
- `result/notebooks/data_management/` (utility notebooks)
- `result/data_agent/` (optional: agent_instructions.md, data_source_description.md, data_source_instructions.md, query_examples.json)
- `result/warehouse/` (bij SP-gedreven scenario's, SQL procedures)
- `fabric_workspace/lakehouses/<prefix>_bronze.Lakehouse/`, `<prefix>_silver.Lakehouse/`, `<prefix>_gold.Lakehouse/` (na deployment)
- `fabric_workspace/notebooks/` (Fabric Git Integration format, na deployment)
- `tests/` (validatie notebooks)

### 5. CDC or CDF besluitregels
- Kies CDF wanneer SQL 2025 CDF beschikbaar en stabiel is
- Kies CDC als CDF niet beschikbaar is
- Kies incremental batch alleen als beide ontbreken, met expliciete risicoacceptatie

### 6. Monitoring minimum
- Pipeline and notebook run status
- Row count and key quality checks
- Freshness and lag checks
- Alerts en incident logging in evidence

## Output Expectations

- Plan met expliciete laagmapping
- Onderbouwde ingestionkeuze met kostenmotivering
- Deploybare Jupyter notebooks (`.ipynb`) in `result/notebooks/` met:
  - Geïncorporeerde metadata en comments uit bronbestanden
  - Business rules en transformatielogica gedocumenteerd in Markdown cellen
  - Verduidelijkende comments bij complexe code
  - Bronverwijzingen voor traceerbaarheid
- Optional: Data Agent artifacts in `result/data_agent/` (als bevestigd in intake)
- Test en monitoring evidence
