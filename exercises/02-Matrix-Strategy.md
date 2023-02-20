# Matrix Strategy Workflows

Defining a matrix strategy in a workflow allow you to scale out job execution based off of variables you define. This is incredibly useful for repeating the same job but with slightly different values, like if you needed to test software against different versions of a language, or build software against multiple operating systems.

The below example walks you through using a matrix strategy to execute jobs on different runners.
**GitHub Actions** run off of workflow files that are managed and maintained in your repository. You can see workflow executions and their job history from the Actions tab on your repository. Additionally, you can see the status of those executions (successful, failing, running).

Removing a workflow from your repository does not remove it's history either. Logs are retained based off of the settings in your enterprise & organization.

## Step 1: Modify your existing workflow

1. From the **feature/basic-workflow** branch of your repository, find the file named `.github/workflows/basic-workflow.yaml`
2. Copy the contents below to the file:

```yaml
name: Basic Workflow
on:
  push:
    branches: feature/basic-workflow
jobs:
  do-things:
    name: Do Things
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [macos-latest, windows-latest]
    steps:
      - name: The Thing I've Done
        run: echo "I've done a thing!"
```

3. Add & commit your changes, then publish your branch.
4. Go to your repository, and view the Actions tab to see the execution against your published branch.

The result will be an execution of not just one job, but three. The strategy defined one variable with three values (matrix.os) that were then used to scale out. For each variable value you add, the number of jobs increases.

In this example, the `run` property is used to execute a simple CLI command. In our case, we are using Linux (ubuntu) and therefore are writing bash commands.

## Step 2: Scale out the matrix strategy even further
Now we will add another `matrix` variable called `node-version` and use that to scale out even further.

1. Replace the contents of the workflow file from the previous step:

```yaml
name: Matrix Strategy
on:
  push:
    branches: feature/basic-workflow
jobs:
  do-things:
    name: Do Things In A Matrix - ${{ matrix.os }}, ${{ matrix.node-version }}
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [macos-latest, windows-latest]
        node-version: [14.x, 15.x]
    steps:
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
```

4. Add & commit your changes, then push your branch.
5. Go to your repository, and view the Actions tab to see the execution.

The result will be twelve concurrent jobs running across the different runners and setting up the corresponding versions of node.

## Step 3: Clean Up
1. Delete the published branch created in [Step 1](#step-1-create-a-matrix-workflow-to-scale-across-runners)
2. Switch back to the default branch locally.
