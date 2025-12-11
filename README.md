# expertperitoapp2.0

This repository hosts public releases for the Expert Perito application, which are automatically created from the [expertrevolution.app.periti.crossplatform.v2](https://github.com/Expert-Revolution/expertrevolution.app.periti.crossplatform.v2) repository.

## Automated Release Workflow

The repository includes a GitHub Actions workflow that creates public releases automatically. The workflow can be triggered in two ways:

### 1. Via Repository Dispatch (Recommended for Automation)

The workflow can be triggered from another repository using the `repository_dispatch` event. This is the recommended method for automated cross-repository release creation.

**Required Payload:**
```json
{
  "event_type": "create-release",
  "client_payload": {
    "tag": "v1.0.0",
    "version": "1.0.0",
    "artifact_name": "release-artifact",
    "source_repo": "Expert-Revolution/expertrevolution.app.periti.crossplatform.v2",
    "run_id": "1234567890"
  }
}
```

**Example: Triggering from Source Repository**

Add this step to your workflow in the source repository:

```yaml
- name: Trigger release in public repository
  uses: peter-evans/repository-dispatch@v2
  with:
    token: ${{ secrets.PAT_TOKEN }}
    repository: Expert-Revolution/expertperitoapp2.0
    event-type: create-release
    client-payload: |
      {
        "tag": "${{ github.ref_name }}",
        "version": "${{ github.ref_name }}",
        "artifact_name": "release-artifact",
        "source_repo": "${{ github.repository }}",
        "run_id": "${{ github.run_id }}"
      }
```

**Note:** You need a Personal Access Token (PAT) with `repo` scope stored as `PAT_TOKEN` secret to trigger workflows in other repositories. For cross-repository artifact downloads, the workflow may also need a PAT token instead of the default GITHUB_TOKEN.

### 2. Via Manual Workflow Dispatch

The workflow can also be triggered manually from the GitHub Actions UI:

1. Go to Actions tab
2. Select "Create Public Release" workflow
3. Click "Run workflow"
4. Fill in the required inputs:
   - **tag**: Release tag (e.g., v1.0.0)
   - **version**: Release version
   - **artifact_name**: (Optional) Name of the artifact to download
   - **source_repo**: (Optional) Source repository
   - **run_id**: (Optional) Workflow run ID for artifact download

## Workflow Features

- ✅ Creates public releases with specified tag and version
- ✅ Downloads artifacts from source repository workflow runs
- ✅ Attaches artifacts to the release
- ✅ Automatically generates release notes
- ✅ Supports both automated and manual triggering

## Requirements

For cross-repository artifact downloads, ensure:
1. The source repository has uploaded artifacts in the specified workflow run
2. The artifact name matches the one specified in the payload
3. Proper GitHub token permissions are configured