# Private Reusable Workflows for Maven Projects

This repository contains private reusable GitHub Actions workflows for building, testing, and notifying completion of Maven projects.

## Available Workflows

### 1. Maven Build, Test, and Notify (`maven-build-test.yml`)

Builds and tests a Maven project, then sends a notification webhook upon completion.

**Inputs:**
- `java-version` (optional, default: '17'): Java version to use
- `java-distribution` (optional, default: 'temurin'): Java distribution
- `artifact-name` (optional, default: 'test-results'): Name for test results artifact
- `webhook-url` (optional): Webhook URL for completion notifications (required for notifications)
- `template-repo` (optional): Template repository containing test files (e.g., 'your-org/template-repo')
- `template-tests-path` (optional, default: 'src/test/java'): Path to tests in template repository
- `template-ref` (optional, default: 'main'): Branch/tag/commit to checkout from template repo

**Secrets:**
- `WEBHOOK_URL` (optional): Webhook URL securely provided (takes precedence over input)
- `TEMPLATE_REPO_TOKEN` (optional): Token to access private template repository

**Features:**
- Builds Maven project with error reporting
- Runs tests and uploads results as artifacts
- **Loads tests from a template repository** (optional)
- Automatically notifies webhook endpoint with build status
- Sends detailed information including repository, branch, commit, and run details

## Usage in Cloned Repositories

### With Template Repository Tests (Recommended for Assignments)

This is useful when you want to keep test files separate from student code:

```yaml
name: Build and Test Maven Project

on:
  workflow_dispatch:
  push:

jobs:
  build-test-notify:
    uses: your-org/autocode-reusable-workflows/.github/workflows/maven-build-test.yml@main
    with:
      java-version: '17'
      template-repo: 'your-org/java-assignment-template'
      template-tests-path: 'src/test/java'
      template-ref: 'main'
    secrets:
      WEBHOOK_URL: ${{ secrets.WEBHOOK_URL }}
      TEMPLATE_REPO_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Basic Usage with Notification

Create `.github/workflows/build.yml` in your cloned repository:

```yaml
name: Build and Test Maven Project

on:
  workflow_dispatch:
  push:
    branches: [ main ]

jobs:
  build-test-notify:
    uses: your-org/autocode-reusable-workflows/.github/workflows/maven-build-test.yml@main
    with:
      java-version: '17'
      artifact-name: 'test-results'
      webhook-url: 'https://your-webhook-endpoint.com/workflow/results'
    secrets:
      WEBHOOK_URL: ${{ secrets.WEBHOOK_URL }}
```

### Using Secret for Webhook URL (Recommended)

```yaml
name: Build and Test Maven Project

on:
  workflow_dispatch:
  push:

jobs:
  build-test-notify:
    uses: your-org/autocode-reusable-workflows/.github/workflows/maven-build-test.yml@main
    with:
      java-version: '17'
    secrets:
      WEBHOOK_URL: ${{ secrets.WEBHOOK_URL }}
```

### Custom Java Version

```yaml
jobs:
  build-java-21:
    uses: your-org/autocode-reusable-workflows/.github/workflows/maven-build-test.yml@main
    with:
      java-version: '21'
      java-distribution: 'temurin'
    secrets:
      WEBHOOK_URL: ${{ secrets.WEBHOOK_URL }}
```

### Custom Test Path in Template

If your tests are in a different location:

```yaml
jobs:
  build-with-custom-tests:
    uses: your-org/autocode-reusable-workflows/.github/workflows/maven-build-test.yml@main
    with:
      template-repo: 'your-org/java-assignment-template'
      template-tests-path: 'tests'  # Custom path
      template-ref: 'v1.0.0'        # Use a specific version
    secrets:
      WEBHOOK_URL: ${{ secrets.WEBHOOK_URL }}
      TEMPLATE_REPO_TOKEN: ${{ secrets.PAT_TOKEN }}
```

### Without Notifications

To skip notifications, simply don't provide `webhook-url` input or `WEBHOOK_URL` secret:

```yaml
jobs:
  build-only:
    uses: your-org/autocode-reusable-workflows/.github/workflows/maven-build-test.yml@main
    with:
      java-version: '17'
      template-repo: 'your-org/java-assignment-template'
```

**Note:** The notification job will run but skip sending if neither `webhook-url` input nor `WEBHOOK_URL` secret is provided.

## Template Repository Setup

### Structure

Your template repository should have tests organized like this:

```
your-org/java-assignment-template/
├── src/
│   └── test/
│       └── java/
│           └── com/
│               └── example/
│                   ├── CalculatorTest.java
│                   └── StringUtilsTest.java
└── README.md
```

### Access Requirements

- **Public template repo**: No additional setup needed
- **Private template repo**: Use `TEMPLATE_REPO_TOKEN` secret with a Personal Access Token (PAT) that has `repo` scope

### Best Practices

1. **Version your tests**: Use tags or branches for different test versions
2. **Keep tests separate**: Don't include student code in template repo
3. **Document test requirements**: Add README explaining what students need to implement

## Webhook Notification Payload

The workflow sends the following JSON payload to the webhook:

```json
{
  "status": "completed",  // or "failed"
  "repository": {
    "name": "owner/repo-name",
    "owner": "owner"
  },
  "branch": "main",
  "commit": "abc123...",
  "runId": "123456789",
  "runNumber": "42",
  "conclusion": "success"  // or "failure", "cancelled", etc.
}
```

## Setup Instructions

1. **Set up this repository:**
   - Ensure this repository is accessible to your organization
   - Go to Settings → Actions → General
   - Under "Access", select "Accessible from repositories in the 'your-org' organization"

2. **Set up template repository (if using):**
   - Create a repository for your test files
   - Organize tests in the appropriate directory structure
   - If private, ensure it's accessible to your organization

3. **In each cloned repository:**
   - Create `.github/workflows/build.yml` using the template
   - Replace `your-org` with your GitHub organization name
   - Add `WEBHOOK_URL` secret in repository Settings → Secrets and variables → Actions
   - Set `WEBHOOK_URL` to your notification endpoint
   - (Optional) Add `TEMPLATE_REPO_TOKEN` if using a private template repository

4. **Test the workflow:**
   - Trigger manually via Actions tab → Build and Test Maven Project → Run workflow
   - Or push to main branch

## Notes

- The reusable workflow combines build, test, and notification in a single workflow
- Tests from template repository are copied before running Maven build
- Notifications are sent regardless of build success or failure
- For private reusable workflows, calling repositories must be in the same organization
- Use `@main` to reference the latest version, or use a specific tag/commit for stability
- The `workflow_run` trigger cannot be used with reusable workflows, so notification is integrated into the main workflow

## Migration from Old Workflows

If you previously had separate "Build and Test" and "Notify Completion" workflows:

**Old approach (2 workflows):**
- `build-test.yml` - Build and test
- `notify.yml` - Triggered by `workflow_run`

**New approach (1 workflow):**
- Single workflow that builds, tests, and notifies

Simply replace both old workflows with the new template, and the notification will happen automatically after build completion.
