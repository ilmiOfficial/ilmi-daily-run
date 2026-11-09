# ilmi daily run

A GitHub Actions schedule that runs the daily data pipeline of [ilmi](https://ilmi.live) on a GitHub runner, so the site is refreshed every day without anyone's computer being on.

The repository is public because GitHub does not bill standard runners for a public repository. It holds only the workflow in `.github/workflows/daily.yml` and this file: no data and no secret value. The code it runs is checked out from a private repository with a read and write deploy key, and the secrets are repository secrets.

## What a run does

1. At 02:23 and again at 08:23 UTC it checks whether the live site already serves today's data. If it does, it stops. The second time exists because GitHub delays and sometimes drops scheduled runs.
2. Otherwise it runs the same script a person runs by hand: scrape, clean, check, commit the data, build the site, deploy it, and write the Neon copies.
3. The public log shows only the step headers and error lines. The full log goes to a private branch.
4. A failure sends a Telegram message to the owner.

## Start one by hand

Actions, "ilmi daily run", Run workflow:

| Mode | Does |
|---|---|
| `full` | the daily run |
| `no-scrape` | rebuilds and deploys what the private repository holds, without scraping |
| `probe` | checks the runner, the deploy key and which secrets are missing, and changes nothing |

"Force" runs a full run even if the site is already current.

## Limits that were measured or are known

* A GitHub job ends after 6 hours. The pipeline needs about 2.
* GitHub switches scheduled workflows off in a public repository after 60 days without activity. Every run pushes a small `heartbeat` branch to this repository to count as activity. Whether GitHub counts that push is not proven yet.
* The tables that cannot be rebuilt are not backed up by this run, because the runner's disk is thrown away. That backup is a job of the owner's own machine.

The source of this repository is `scripts/cloud/runner-repo/` in the private ilmi repository.
