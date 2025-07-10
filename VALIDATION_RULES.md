JIRA-001: Inventory Existing Identity & Infra Artifacts Across All Tenants
Points: 2
Title: Inventory and document app registrations, service accounts, vaults, and file shares
Description:
Audit and document the resources related to custom RBAC and statefile storage left behind by the laid-off engineer across all three tenants (dev, QA, prod), including:

App registrations in Azure AD (per tenant)

On-prem service accounts (proid accounts for dev/qa/prod)

Vault references (SCV or other, if any exist)

Network file shares used for Terraform state

Acceptance Criteria:

A confluence or markdown document exists listing all discovered:

Azure app registrations

On-prem proid service accounts

Secrets vaults or SCV entries

File share locations (e.g., \\smb\terraform\state)

Unknowns or missing resources are clearly marked with next steps or assumptions

JIRA-002: Initialize GitHub Repo & Store Secrets
Points: 1
Title: Clone existing RBAC repo and bootstrap new GitHub repo with secrets
Description:
Set up a new GitHub repository for the RBAC controller work by cloning the prior implementation, and initialize GitHub Actions with needed secrets.

Acceptance Criteria:

New GitHub repo is created and contains the cloned RBAC repo contents

GitHub Actions secrets MSDEV_APP_SECRET, MSADQA_APP_SECRET are added

README updated with high-level setup instructions

JIRA-003: Test Custom Role Creation via Terraform and Plan for Pipeline
Points: 2
Title: Use msadqa app registration to create a custom read-only RBAC role in Terraform
Description:
Use the existing msadqa app registration to deploy a test custom role definition in the QA tenant using Terraform. Document any issues faced and consider what pipeline-based deployment would require (very high level).

Acceptance Criteria:

Attempted deployment of a custom read-only role using TF and msadqa app

Challenges, permissions issues, or gaps documented in a shared notes file

High-level notes on what a CI/CD pipeline (e.g., GitHub Actions) would need to handle this reliably
