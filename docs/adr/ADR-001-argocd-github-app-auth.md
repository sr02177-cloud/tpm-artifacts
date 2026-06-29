# ADR -001: Migrate Argo CD Repository Authentication from PAT-Based to GitHub App-Based Authentication

## Status
Accepted

## Date
2026

## Context
Our platform engineering team managed Argo CD deployments across multiple organizations within a large enterprise environment. Repository authentication was handled via Personal Access Tokens (PATS) stored as Kubernetes secrets on an AKS Cluster.

This approach introduced several operational risks & toil:
- Tokens require manual annual rotation across multiple Github organizations creating a recurring operational burden and audit risk
- There was no scalable mechanism to alert on upcoming expiration without custom monitoring.

The risk was compounded by the multi-org nature of the environment. A single missed rotation could silently break GitOps sync across hundreds of deployments with no immediate alerting.

## Decision
Migrate Argo CD repository authentication from PAT-based to GitHub App-based authentication across all connected GitHub organizations.

Github Apps do not require manual rotation, as the credentials are short lived tokens generated on demand, thus requiring no manual rotations. GitHub Apps authenticate as an installation and require only a one time installation across the required organizations.

## Options Considered

## Option 1: Automate PAT Rotation
Implement a pipeline to automatically rotate PATS on a schedule & updated the kubernetes secret.
**Pros:**
- lower initial effort
- no changes to Argo CD configurations.

**Cons:**
- Adds a pipleine complexity and a new failure mode
- Treats the symtpon, not the cause

## Option 2: Migrate to GitHub App Authentication (Selected)
Create a GitHub App, install it across the organizations, and configure Argo CD to authenticate using the App's Private key stored in the azure key vault.

**Pros:**
- no manual rotation - short-lived tkens generated automatically
- Fine grained, installation-scoped permissions
- Aligns with Github's recommended approach for CI/CD integration
- Github app can be installed once on all in scope repos by in-house github team.

- **Cons:**
- higher initial setup effort
  
## Implementation

### Components Involved
**Argo CD**
**Azure Key Vault**
**AKS**
**Github**

### Steps
1. Created a GitHub app at the organizational level with repo read permissions.
2. Generated a private key and stored it in Azure Key Vault
3. Installed the GitHub app across all required GitHub organizations by inhouse GitHub team.
4. Configured Argo CD repository credentials using the Github App.
5. Validated sync across all connected repositories.
