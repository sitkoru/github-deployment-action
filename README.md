# github-deployment-action

Action to manage GitHub Deployments

# Usage

## Create

```yml
name: Release

on:
  release:
    types:
      - released
  workflow_dispatch:

jobs:
  build:
    name: Build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Prepare
        id: prep
        shell: bash
        run: |
          VERSION=${GITHUB_REF#refs/tags/}
          echo ::set-output name=version::${VERSION}
      # some build actions
      - uses: sitkoru/github-deployment-action@v1
        name: Create GitHub deployment
        with:
          token: ${{ secrets.BOT_TOKEN }}
          environment: production
          ref: ${{ github.ref }}
          version: ${{ steps.prep.outputs.version }}
```

## Change status

```yml
name: "Deploy"
on:
  deployment:

jobs:
  deploy:
    name: Deploy
    runs-on: ubuntu-latest
    steps:
      # some deploy actions

      - name: Update deployment status (success)
        if: success()
        uses: sitkoru/github-deployment-action@v1
        with:
          token: ${{ secrets.BOT_TOKEN }}
          state: "success"
          deployment_id: ${{ github.event.deployment.id }}

      - name: Update deployment status (failure)
        if: failure()
        uses: sitkoru/github-deployment-action@v1
        with:
          token: ${{ secrets.BOT_TOKEN }}
          state: "failure"
          deployment_id: ${{ github.event.deployment.id }}
```