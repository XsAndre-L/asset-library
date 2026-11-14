# Public GHCR publishing

Every push to `xserver` builds the Dockerfile and publishes a Linux AMD64 image
at `ghcr.io/xsandre-l/asset-library`. The active workflow is named
**XServer Docker Image**. Other branches and tag pushes do not publish through it.
Publishing an image does not restart or deploy an XServer service.

The original workflow remains unchanged in
`.github/workflows/docker-image.yaml.disabled`, so it cannot run on this branch.

## Authentication and visibility

The workflow uses the built-in `GITHUB_TOKEN` with `contents: read` and
`packages: write`. No Docker Hub variables, Docker Hub token, or personal access
token is needed for publishing. GitHub Actions must be enabled for the fork.

The existing package is public and can be pulled without authentication. The
workflow preserves that visibility. If package publishing access is removed,
grant this repository Actions access in the package settings.

Find the image under your GitHub profile's **Packages** tab.

## Published versions

- `ghcr.io/xsandre-l/asset-library:xserver`: the latest successful branch build.
- `ghcr.io/xsandre-l/asset-library:sha-<full-commit-sha>`: identifies its source.

Prefer the published digest for a stable deployment. Rebuilding the same source
can change its base image or downloaded packages. No `latest` tag is published.

The manual trigger is also defined, but GitHub normally exposes manual dispatch
only when the workflow exists on the default branch. Push triggers work directly
on `xserver`; merging this workflow into `main` is not required for push builds.

## Pull into XServer

No GitHub token or Docker login is needed to pull this public image.

Set the XServer service's ignored `.env` image value:

```dotenv
ASSET_LIBRARY_IMAGE=ghcr.io/xsandre-l/asset-library:xserver
```

From the XServer checkout, pull the image and start the local service:

```sh
docker compose --env-file server/studio/asset-library/.env -f server/studio/asset-library/compose.yml pull
bun run asset-library
```

Production still follows XServer's separate deployment and backup workflow.
Image privacy does not change access to the running asset-library website.
