# AutoCode Reusable Workflows

Reusable GitHub Actions workflows for student Maven projects.

## Reusable Workflow

### Maven Build and Test (`maven-build-test.yml`)

Builds, tests Maven projects and optionally sends webhook notification.

**Inputs:**
| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `java-version` | JDK version | No | `17` |
| `java-distribution` | JDK distribution | No | `temurin` |
| `artifact-name` | Test results artifact name | No | `test-results` |
| `webhook-url` | Webhook URL for completion notification | No | `''` (disabled) |

## Student Setup

Copy `caller-workflows/build.yml` to your repo's `.github/workflows/` and replace `YOUR_ORG/autocode-reusable-workflows` with the actual path.

## 🔐 Private Repository Access

For private repos, student repos must be in the **same organization** and you must enable:
**Settings** → **Actions** → **General** → **Access** → **"Accessible from repositories in the organization"**

> **Tip**: Make this repo **public** for easiest access.

## Structure

```
.github/workflows/
└── maven-build-test.yml    # Reusable workflow

caller-workflows/
└── build.yml               # Example caller (copy to student repos)
```

