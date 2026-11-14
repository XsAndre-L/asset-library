# Private Docker Hub publishing

Every push to `xserver` builds the Dockerfile and publishes a Linux AMD64 image
to Docker Hub. Other branches and tag pushes do not publish through this workflow.
Publishing does not restart or deploy an XServer service.

The original GHCR workflow is preserved unchanged as
`.github/workflows/docker-image.yaml.disabled`. Its disabled extension prevents
GitHub Actions from running it. The active workflow is
`.github/workflows/publish-xserver-image.yml`, named **XServer Docker Image**.

## One-time setup

1. On Docker Hub, create `asset-library` in your account and explicitly select
   **Private**. Verify its visibility before adding GitHub credentials. The workflow
   does not set visibility; do not rely on a push to create a private repository.
2. Create a Docker Hub access token with Read and Write permission. Keep it out of
   source files and chat messages.
3. In this GitHub repository, open **Settings > Secrets and variables > Actions**.
   Add repository variables `DOCKERHUB_USERNAME` (your Docker ID) and
   `DOCKERHUB_REPOSITORY` (the full name, such as `your-docker-id/asset-library`).
   Add a repository secret `DOCKERHUB_TOKEN` containing the access token.
4. Enable GitHub Actions for the fork if disabled. Push to `xserver` and watch
   **Actions > XServer Docker Image**. If a run failed because
   settings were missing, configure them and rerun that failed run.

The manual trigger is also defined, but GitHub normally exposes manual dispatch
only when the workflow exists on the default branch. Push triggers work directly
on `xserver`; merging this workflow into `main` is not required for those triggers.

## Published versions

Each successful run publishes two tags:

- `your-docker-id/asset-library:xserver`: the most recently published branch build.
- `your-docker-id/asset-library:sha-<full-commit-sha>`: identifies the source revision.

For deployment, prefer the image digest shown in the build output. A commit tag
identifies source, but reruns can rebuild it using changed base images or packages.
No `latest` tag is published.

## Pull into XServer

On the target server, run `docker login --username YOUR_DOCKER_ID` and use a
Docker Hub token with Read permission at the password prompt. Use the same Docker
user/context that starts Compose. Do not put registry tokens in Compose or `.env`.

Set the XServer service's ignored `.env` image value:

```dotenv
ASSET_LIBRARY_IMAGE=your-docker-id/asset-library:xserver
```

Then, from the XServer checkout, pull the image and start the local service:

```sh
docker compose --env-file server/studio/asset-library/.env -f server/studio/asset-library/compose.yml pull
bun run asset-library
```

Production deployment still follows XServer's separate deployment and backup
workflow. Publishing an image does not change access to the running website.
