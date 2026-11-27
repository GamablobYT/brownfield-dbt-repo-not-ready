# property_ops (not ready) — sample brownfield dbt repository for Epic 2

The same small dbt project as `samples/brownfield-dbt-repo`, but **not ready for source onboarding**: it is what a
team's existing repository looks like before a repository onboarding ticket. Synthetic data only.

| Check | State here | What the repository onboarding ticket adds |
| --- | --- | --- |
| 3 dbt project | passes (`dbt_project.yml`, `dbt parse` succeeds) | — |
| 4 AGENTS.md managed block | **fails**: `AGENTS.md` has hand-written instructions but no `<!-- bmad:context -->` block (and a `CLAUDE.md` that contradicts it) | Winston adopts: ledger of every instruction, the managed block |
| 5 onboarding knowledge | **fails**: no `docs/onboarding/` | Mary derives, Winston checks |
| 6 BMad config | **fails**: no `_bmad/config.toml` | Amelia adds it and its output folders |
| 7 CI runs dbt build | **fails**: `.github/workflows/ci.yml` runs `dbt run` + `dbt test`, never `dbt build` | Amelia adds a new GDP workflow fitted to it |

Use `samples/brownfield-dbt-repo` instead when you want a repository that is already Ready (Epic 1 flow).

## Try Epic 2 with it

1. Create an empty GitHub repository in an allowed organisation and push a copy of this folder to `main`
   (`git init -b main && git add . && git commit -m "property_ops" && git remote add origin … && git push -u origin main`).
2. Protect `main` (branch protection rule or ruleset) and install the app's GitHub App with Administration: read
   and **Workflows: read and write** (checks 1 and 2 must pass before the ticket can open).
3. In the app link the repository to a Jira project: the checklist shows "Not ready · 4 checks failing" (4–7) and
   offers **Open repository onboarding ticket**. Open it, send a first message, and follow Winston → Mary →
   Winston → Amelia; Amelia opens the pull request; when CI is green and checks 3–7 pass on its head the ticket is
   Done; merge it in GitHub and the checklist turns "Ready · 7 of 7 checks pass".
