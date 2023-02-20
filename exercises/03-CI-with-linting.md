# CI Workflow with Linting

Linting your code base is an essential step in setting standards for your code and workflows. It should be ran as part of the CI step.

The below example walks you through adding the SuperLinter action into your workflow. 

## Step 1: Implement the Linter to an existing workflow
First, let's take advantage of the other workflows that we have made in the earlier examples. 

1. From the **default** branch of your repository, create a new branch of code called `feature/ci`
2. Modify the file named `.github/workflows/basic-workflow.yaml`
3. Copy the contents below to the file:

```yaml
name: CI Linting Workflow
on:
  push:
    branches: feature/ci
jobs:
  do-things:
    name: Do Things
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [macos-latest, ubuntu-latest, windows-latest]
    steps:
      - name: The Thing I've Done
        run: echo "I've done a thing!"
  build:
    # Name the Job
    name: Lint Code Base
    # Set the agent to run on
    runs-on: ubuntu-latest

    ##################
    # Load all steps #
    ##################
    steps:
      ##########################
      # Checkout the code base #
      ##########################
      - name: Checkout Code
        uses: actions/checkout@v3
        with:
          # Full git history is needed to get a proper
          # list of changed files within `super-linter`
          fetch-depth: 0

      ################################
      # Run Linter against code base #
      ################################
      - name: Lint Code Base
        uses: github/super-linter@v4
        env:
          VALIDATE_ALL_CODEBASE: false
          DEFAULT_BRANCH: feature/ci
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

4. Add & commit your changes, then publish your branch.
5. On your Pull request is submitted, you should notice the Linter begin shortly. Feel free to click on icon generated in the PR to view the Linter in action.
6. If not, go to your repository, and view the Actions tab to see the execution against your published branch.

The result will be that the linter is currently running and going through your code base. Do not be surprised if the test fails. You can take this as an opportunity to read the logs and determine where the fix can go.

## Step 2: Clean Up
1. Delete the published branch created in [Step 1](#step-1-implement-the-linter-to-an-existing-workflow)
2. Switch back to the default branch locally.
