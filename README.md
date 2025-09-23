# Best practices for publishing with pnpm, changesets and oidc on github actions

This repository demonstrates a release workflow on github actions that uses minimal permissions for different steps

## Jobs

### Checks

First ensure that a new release can be tagged

### Changesets

Now either create/update a release PR or tag new versions to be published

> This deviates from the common changesets flow where you'd build and publish in this job

### Build

only run when the previous Changesets job emitted that it wants to publish some packages.
builds the packages, packs them and uploads the packages for consumption in the next job
The script is structured in a way to ensure that build can not modify git content to be packed.

> This job only has the `contents: read` permission, so it cannot change git state or execute a publish

### Publish

receives the .tgz files from Build via download action. Uses an sha256sum to ensure integrity for files received.
Extracts publishing tag from changesets config and runs `pnpm publish some-package-1.3.4.tgz` for each tgz.

> This job only has `contents: read` and `id-token: write` and does not install dependencies.
