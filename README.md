# shannon-ops-review — staging

A preview of the [shannon-ops-review](https://github.com/rahuls17-cell/shannon-ops-review)
dashboard, so a change can be looked at on a real deploy before it reaches the
one people use.

**https://rahuls17-cell.github.io/shannon-ops-review-staging/**

This repository holds no dashboard code and no data. It holds one workflow,
which fetches both at build time.

## Why it is a separate repository

GitHub Pages serves one site per repository, and the dashboard's Pages site is
built by a workflow on its `main` branch. Publishing a second branch from there
would replace the live site — the one the client reads
`assets/pipeline-truth.json` from.

Keeping the build here means a broken staging build cannot reach production.
Nothing here has write access to the dashboard repository, and nothing there
triggers anything here.

## How to use it

1. Push the change to the `staging` branch of the dashboard repository.
2. Run **Deploy staging** in the Actions tab, or:

   ```
   gh workflow run deploy-staging.yml --repo rahuls17-cell/shannon-ops-review-staging
   ```

3. Open the URL above.

Both inputs are optional. `ref` previews any branch or SHA, not just `staging`,
so a one-off can be looked at without pushing it anywhere permanent. `data_ref`
chooses which ref the data snapshot comes from.

## Code and data come from different places

| | from | |
|---|---|---|
| code | the ref being previewed | `staging` by default |
| data | whatever is on `main` at build time | copied once per run |

So a deploy is a **snapshot**: the figures freeze at the moment you run it and
stay put until you run it again. That is deliberate — the production site is
rebuilt by the VM every ten minutes, and numbers moving underneath you while you
are comparing a change is what makes a comparison worthless. Run the workflow
again for fresh data.

It also means staging never has to merge `main` to get current data, so the
staging branch stays a clean diff of whatever is being tried.

## It is marked, on purpose

Every build injects a banner naming the code and data commits, sets
`noindex, nofollow`, and writes a `robots.txt` that disallows everything. The
banner is injected at build time rather than committed, so the staging branch
stays a clean copy of `main` — a banner committed to the branch would eventually
be merged into production.

## What it is not

The data is a copy of what production already publishes, including payout
figures and trainer addresses. This site is public, the same way production is.
It does not create a second private copy of anything, and it is not somewhere to
put data that should not be public.
