# Testing the Release Workflow

This document explains how to test the cross-repository release workflow.

## Testing Scenarios

### Scenario 1: Manual Test (workflow_dispatch)

This is the simplest way to test the workflow without setting up cross-repository automation.

**Steps:**
1. Go to the Actions tab in this repository
2. Select "Create Public Release" workflow
3. Click "Run workflow"
4. Fill in the required inputs:
   - **tag**: `v0.1.0-test`
   - **version**: `0.1.0-test`
   - Leave other fields empty for now
5. Click "Run workflow"
6. Monitor the workflow execution
7. Check if a release was created in the Releases section

**Expected Result:**
- A new release with tag `v0.1.0-test` should be created
- Release name should be "Release 0.1.0-test"
- Release should not have any artifacts (since we didn't specify run_id)

### Scenario 2: Test with Artifacts (workflow_dispatch)

To test artifact download functionality:

**Prerequisites:**
- Have a workflow run in the source repository that uploaded artifacts
- Note the run ID from that workflow

**Steps:**
1. Go to Actions tab
2. Select "Create Public Release" workflow
3. Click "Run workflow"
4. Fill in all inputs:
   - **tag**: `v0.2.0-test`
   - **version**: `0.2.0-test`
   - **artifact_name**: Name of the artifact in source repo
   - **source_repo**: `Expert-Revolution/expertrevolution.app.periti.crossplatform.v2`
   - **run_id**: The workflow run ID from source repository
5. Click "Run workflow"

**Expected Result:**
- Release created with tag `v0.2.0-test`
- Artifacts from source repository should be attached to the release

### Scenario 3: Automated Test (repository_dispatch)

To test the full automated workflow:

**Prerequisites:**
- PAT token configured in source repository (see SETUP.md)
- Workflow added to source repository

**Steps:**
1. Trigger a workflow in the source repository that:
   - Uploads artifacts
   - Sends repository_dispatch to this repository
2. Monitor the Actions tab in this repository
3. Workflow should trigger automatically
4. Check the created release

**Expected Result:**
- Workflow triggers automatically via repository_dispatch
- Release created with correct tag and version
- Artifacts attached from source repository

## Troubleshooting Tests

### Workflow doesn't appear in Actions tab
- Ensure the workflow file is in the default branch (main/master)
- Check that the YAML syntax is valid
- Wait a few minutes for GitHub to detect the workflow

### Permission errors when creating release
- Check that the workflow has `contents: write` permission
- For repository_dispatch, ensure the PAT token has `repo` scope

### Artifacts not downloading
- Verify the run_id is correct and from the source repository
- Check that artifacts exist in that workflow run
- Ensure artifact names match exactly
- Verify the GITHUB_TOKEN has access to the source repository

### Release already exists error
- Use unique tags for each test (e.g., v0.1.0-test, v0.2.0-test, etc.)
- Delete previous test releases before re-testing with the same tag

## Cleanup After Testing

To clean up test releases:

1. Go to the Releases section
2. Click on each test release
3. Click "Delete this release"
4. Optionally delete the tags:
   ```bash
   git push --delete origin v0.1.0-test
   git push --delete origin v0.2.0-test
   ```

## Next Steps

After successful testing:
1. Document any issues found
2. Update the workflow if needed
3. Set up the automation in the source repository
4. Monitor the first production release
