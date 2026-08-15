# Enterprise CI/CD for MuleSoft CloudHub 2.0 using GitHub Actions & Anypoint CLI

This repository is a template project to showcase enterprise-grade CI/CD workflows using GitHub Actions and Anypoint CLI for MuleSoft CloudHub 2.0 deployments.

## GitHub Actions Setup

This template relies on GitHub Actions secrets and repository variables.

### GitHub Actions environment secrets

There are two environments created in GitHub. For each environment, **SANDBOX** and **PRODUCTION**, configure corresponding secrets in GitHub Actions:

- `CONNECTED_APP_ID`
- `CONNECTED_APP_SECRET`
- `ENV_ID`
- `ENV_SECRET`
- `SECURE_KEY`

The `ENV_ID` and `ENV_SECRET` are for API Manager Autodiscovery. The `SECURE_KEY` is just a dummy value in this Demo project, it is here to show how a Mulesoft Encryption Key will be passed.

### GitHub Actions repository variables

Set the following repository variables in GitHub Actions:

- `CH_JAVA_VERSION`: 17
- `CH_MULE_VERSION`: 4.12.0
- `CH_RELEASE_CHANNEL`: EDGE
- `CH_REPLICAS`: 1
- `CH_TARGET`: cloudhub-us-east-2
- `CH_VCORES`: 0.1

### GitHub Actions repository secrets

- `AUTOMATION_PAT`
- `REVIEWER_PAT`

More information on these under **Authentication** heading below.

## Workflows

This repository includes three GitHub Actions workflow files in `.github/workflows`:

- `sandbox.yml`
- `release.yml`
- `release-rollback.yml`
- `security-scan.yml`

### sandbox.yml

The sandbox workflow deploys the application to the SANDBOX environment only. It uses sandbox-specific environment credentials and keeps production isolated.

### release.yml

> **Note:** This project assumes the main branch is named `main`, not `master`. Ensure you change this .yml file accordingly.

The release workflow deploys the application to the PRODUCTION environment only. It uses production-specific credentials and is separated from sandbox deployment to ensure controlled release handling.

### release-rollback.yml

> **Note:** Here the workflow checks out the code using release version tag.

The release rollback workflow rollsback the application in the PRODUCTION environment only. It only modifies, doesn't create a new application.

### security-scan.yml

The security scan workflow scans the repository to detect whether any secrets were accidentally pushed. This is more for personal use than for anyone else copying the project.

## Environment & Branch Rules

Branch protection is enabled on the `main` branch. A pull request with at least one reviewer is required before code can be merged.

To prevent unauthorized production deployments, a deployment protection rule is configured on **PRODUCTION** environment in GitHub to require reviewer approval before the release workflow's **release-prep** and **release-and-deploy** jobs can be started.

## Authentication

This repository belongs to an **organization** account with two members. This is because the main branch protection rule prevents PR merges without approval, and default GitHub rules do not allow self-approval of PRs. To support the release workflow, both users have fine-grained Personal Access Tokens set up and stored as repository secrets:

- `AUTOMATION_PAT`
- `REVIEWER_PAT`

Both users have been assigned an organization role of at least Maintainer. These PATs will have to be approved under organization settings before use.

`AUTOMATION_PAT` Token Permissions:

- Read access to metadata
- Read and Write access to actions, code, and pull requests

`REVIEWER_PAT` Token Permissions:

- Read access to metadata
- Read and Write access to pull requests

## MuleSoft Platform Configuration

On the MuleSoft platform, create two environments named exactly:

- `SANDBOX`
- `PRODUCTION`

Create two connected apps (Client Credentials type), one for SANDBOX and one for PRODUCTION. Each connected app must have **full permissions** for:

- API Manager
- Exchange
- Runtime Manager

Permissions should be scoped to the corresponding environment.

Ofcourse, once you understand this template, it becomes quite easy to tailor it according to multiple environments and different branching strategies. You just need to be creative and let AI take care of the mundane stuff 😉
