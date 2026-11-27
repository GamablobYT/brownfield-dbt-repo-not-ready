# AGENTS.md — property_ops

Instructions for coding agents working in this repository (written by the team, never reviewed).

- Run `dbt run` then `dbt test` before you open a pull request.
- Models live in models/bronze, models/silver and models/gold; prefix them brz_, slv_ and gld_.
- Use the `days_between` macro instead of datediff: DuckDB and Snowflake disagree on it.
- Never commit the profiles.yml file or any data file.
- Ask Priya before adding a new source.
