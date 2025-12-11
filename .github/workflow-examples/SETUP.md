# Setup Guide for Cross-Repository Release Workflow

This guide explains how to set up the automated release workflow between the source repository and the public release repository.

## Prerequisites

1. Access to both repositories:
   - Source: `Expert-Revolution/expertrevolution.app.periti.crossplatform.v2`
   - Public: `Expert-Revolution/expertperitoapp2.0`

2. Administrative access to create secrets and workflows

## Setup Steps

### 1. Create a Personal Access Token (PAT)

1. Go to GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Click "Generate new token (classic)"
3. Set the following:
   - **Note**: "Cross-repo release automation"
   - **Expiration**: Choose an appropriate expiration (90 days recommended)
   - **Scopes**: Select `repo` (Full control of private repositories)
4. Click "Generate token"
5. **Important**: Copy the token immediately (you won't be able to see it again)

### 2. Add PAT to Source Repository Secrets

1. Go to the source repository: `Expert-Revolution/expertrevolution.app.periti.crossplatform.v2`
2. Navigate to Settings → Secrets and variables → Actions
3. Click "New repository secret"
4. Add the secret:
   - **Name**: `PAT_TOKEN`
   - **Value**: Paste the PAT you created in step 1
5. Click "Add secret"

### 3. Add Workflow to Source Repository

Copy the example workflow from `.github/workflow-examples/trigger-from-source-repo.yml` to the source repository at `.github/workflows/create-public-release.yml`.

Customize the workflow based on your build process:
- Add your build commands
- Specify the correct artifact paths
- Adjust the trigger conditions (release, tag push, etc.)

### 4. Test the Workflow

#### Option A: Test via Release
1. Create a new release in the source repository
2. The workflow should trigger automatically
3. Check the Actions tab in both repositories to monitor progress
4. Verify the release appears in the public repository

#### Option B: Test via Manual Trigger
1. Go to Actions tab in the source repository
2. Select your workflow
3. Click "Run workflow"
4. Provide tag and version inputs
5. Monitor the workflow execution
6. Verify the release in the public repository

## Workflow Process

1. **Source Repository**: Build artifacts and upload them
2. **Source Repository**: Trigger `repository_dispatch` event in public repository
3. **Public Repository**: Receive the trigger with metadata (tag, version, run_id)
4. **Public Repository**: Download artifacts from source repository's workflow run
5. **Public Repository**: Create a public release with the artifacts

## Troubleshooting

### Release not created
- Verify the PAT token has `repo` scope
- Check that the PAT hasn't expired
- Ensure the workflow has `contents: write` permission

### Artifacts not attached to release
- Verify the artifact name matches between repositories
- Check that the run_id is correct
- Ensure artifacts were uploaded in the source workflow before triggering dispatch

### Permission errors
- Verify the PAT token has access to both repositories
- Check that the workflow has appropriate permissions in both repos

## Security Notes

- The PAT token should be kept secure and rotated periodically
- Consider using fine-grained PAT tokens with minimal required permissions
- Monitor the token usage in GitHub settings
- Revoke and recreate the token if compromised

## Alternative: Using GitHub App

For enhanced security, consider using a GitHub App instead of a PAT:
1. Create a GitHub App with appropriate permissions
2. Install the app on both repositories
3. Use the app's credentials in the workflow
4. This provides better audit trails and automatic token rotation
