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

Github Apps do not require manual rotation, as the credentials are short lived tokens generated on demand.
