# Reusable Maven Build and Test Workflow Examples

This directory contains example workflows showing how to call the reusable Maven build and test workflow.

## Files

### 1. `caller-workflow-basic.yml`
Basic example with minimal configuration using default settings.

### 2. `caller-workflow-advanced.yml`
Advanced example showing all features:
- Custom Java version
- Template repository for tests
- Webhook notifications

### 3. `caller-workflow-student.yml`
Real-world example for student assignment repositories that pulls test cases from an instructor's template repository.

## Usage

To use the reusable workflow in your repository:

1. Copy one of the example files to `.github/workflows/` in your repository
2. Replace `your-org/autocode-reusable-workflows` with the actual path to this workflow repository
3. Adjust the `@main` reference to use a specific version/tag if desired (e.g., `@v1.0.0`)
4. Configure the inputs and secrets as needed

## Key Features

- **Automatic Test Discovery**: Builds and tests Maven projects automatically
- **Template Tests**: Optionally pull test cases from a separate template repository
- **Error Reporting**: Generates XML reports even when builds fail
- **Webhook Notifications**: Send results to external systems
- **Artifact Upload**: Test results are always uploaded for review

## Required Repository Secrets

If using private template repositories or webhooks, configure these secrets in your repository:

- `TEMPLATE_REPO_TOKEN`: Personal Access Token with repo access (for private templates)
- `WEBHOOK_URL`: URL to receive notification callbacks
- `GH_PAT`: GitHub Personal Access Token (alternative name)

## Inputs Reference

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `java-version` | No | `17` | Java version to use |
| `java-distribution` | No | `temurin` | Java distribution |
| `artifact-name` | No | `test-results` | Name for test results artifact |
| `template-repo` | No | `''` | Template repository (e.g., org/repo) |
| `template-tests-path` | No | `src/test/java` | Path to tests in template |
| `template-ref` | No | `main` | Branch/tag/commit of template |
| `webhook-url` | No | `''` | Webhook URL for notifications |

