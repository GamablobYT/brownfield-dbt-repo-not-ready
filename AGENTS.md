# AGENTS.md — property_ops

Instructions for coding agents working in this repository (written by the team, never reviewed).

<!-- bmad:context -->
<!-- Verified 2026-11-28 against bce6db9. Managed by bmad-project-context; edits inside this block are replaced on refresh. Keep anything you want preserved outside the markers. -->

## property_ops

dbt project (dbt-core, DuckDB locally, Snowflake in production) for lease and rent-roll data, medallion layers bronze → silver → gold. Landed intake is read from source schema `raw`, one table per input file (`models/bronze/_sources.yml`). Data-quality contracts live in `soda/contracts/`.

## Policy

CBRE baseline (version sha256:5f771a625b720c13). Each rule is an interpretation still to be confirmed with CBRE.

- ARCH-01: Data flows bronze (raw, as received) → silver (cleansed) → gold (business-ready); never skip a layer.
- ARCH-02: Never change Phase 1 models, match rules or the Snowflake foundation; new domains add new models only.
- ARCH-03: Reach Reltio only through the Service Layer, fed only from pre-MDM silver; linked-not-mastered domains never call it.
- ARCH-04: Classify every source as contributor (needed and tracked over time: full history) or consumer (neither: ID lineage only).
- ARCH-05: Use only the approved stack: Snowflake, dbt, Soda, Collibra, Airflow, Reltio, FastAPI/WSO2, Kafka.
- ARCH-06: Snowflake objects are `UPPER_SNAKE_CASE`; Enterprise IDs keep their source format (`PROP-######`, `CMP-######`). Model names deviate from the baseline, see Conventions.
- ARCH-07: Prefer views and incremental models; never full-refresh large tables without reason; report compute used per run.
- CDO-01: Never redefine an enterprise L1 class (Property, Company, Lease, Sale, Opportunity, Finance) or copy its attributes (name, address, city, postcode, country) into a new table; link by Enterprise ID and request changes from the enterprise data team.
- CDO-02: Follow enterprise naming, relationship and modelling conventions in segment ontologies.
- CDO-03: Govern ontology extensions under Section D.
- CDO-04: A new data product carries only attributes it owns plus foreign keys to existing products; never duplicate data, request changes from the product owner.
- CDO-05: Build data products on the enterprise platform, aligned to governance, data quality and ontology.
- CDO-06: Conform to enterprise data model standards.
- CDO-07: Consume via Snowflake Data Sharing (preferred) or REST API; every gold data product declares its share.
- CDO-08: Every data product ships a consumer guide, `docs/<product>.md`.
- CDO-09: Govern data products under Section D.
- CDO-10: Search enterprise MDM before creating core master data.
- CDO-11: Migrate segment-specific mastering to enterprise MDM.
- CDO-12: Build new master data on enterprise MDM.
- CDO-13: Govern segment master data under Section D; a linked-not-mastered domain references existing Enterprise IDs (`PROP-*`, `CMP-*`) and never mints its own property or company identifiers.
- CDO-14: Name a business data owner and a data steward for every data product; "TBC" or blank fails.
- CDO-15: Publish ownership, business glossary and one sensitivity class (Public, Internal, Confidential, Restricted) per CDE, with a definition.
- CDO-16: Every gold column traces to a source column (lineage).
- CDO-17: Personal data (names, e-mails, phone numbers) is at least Confidential and has an access rule. Contact records of non-CBRE people (brokers, client contacts, tenants) belong to the Contacts domain: never carry them past bronze, record a dependency instead. A CBRE employee named as owner or assignee follows the Confidential rule.
- CDO-18: State a retention policy: period by classification, archival approach, deletion process.
- CDO-19: Build data quality measurement and remediation for transactional systems on the DQ platform (Soda).
- CDO-20: Every mandatory CDE has at least one Soda quality check; failures land in the DQ control table.
- Never commit `profiles.yml` or any data file; the only exception is synthetic `tests/fixtures/*.csv`. Never copy intake data into the repository, profiles hold statistics only.

## Where things are

- Models: `models/bronze`, `models/silver`, `models/gold`; the shared macro is `macros/days_between.sql`.
- CI fixtures: `tests/fixtures/<table>.csv` is loaded into `raw.<table>` as text by `.github/workflows/ci.yml`; bronze models cast per the column contract.

## Running and verifying

- Run `dbt build` before opening a pull request, without `--full-refresh`. Run it through the GDP app's `dbt_build` tool, never dbt from the shell.
- CI pins Python 3.12, dbt-core 1.12.7, dbt-duckdb 1.11.1 and duckdb 1.5.6; CI currently runs `dbt run` then `dbt test`, not `dbt build`.

## Conventions that differ from defaults

- Name models `brz_<entity>`, `slv_<entity>`, `gld_<entity>` in the matching layer folder; this overrides the ARCH-06 model names.
- Use `property_ops.days_between(start, end)` instead of `datediff`, DuckDB and Snowflake disagree on it and agents keep hard-coding it. Warehouse-specific SQL belongs only in `adapter.dispatch` macros.
- Write SQL in lowercase.

<!-- /bmad:context -->
