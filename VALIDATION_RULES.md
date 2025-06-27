# RBAC Assignment Validation Rules

This document outlines the validation logic required for role assignment requests in the `access-api` project.

---

## 🎯 Goal

Only allow valid Azure role assignments based on organizational policies involving:
- Role restrictions
- Caller and assignee validation via CSA or tags
- Cross-tenant and environment alignment
- Resource group metadata
- TAM policy integration (stubbed)

---

## ✅ Identity Definitions

- **Caller SPN**: The service principal making the API call (via bearer token)
- **Assignee SPN**: The identity receiving access (Enterprise App or Managed Identity)
- **Target Resource Group**: The Azure resource group where access is being granted
- **CSA**: Custom Security Attribute on Enterprise Apps
- **MI**: Managed Identity
- **Environment**: One of ["dev", "qa", "prod"]
- **EONID**: Enterprise identifier (e.g. 12345)

---

## 🔐 Token-Level Validation

- Ensure JWT is present
- Ensure `aud` claim matches the self-service app client ID
- Ensure `iss` claim is from a known Entra issuer (e.g., starts with `https://sts.windows.net/`)

---

## ✅ Step-by-Step Validation

### Step 1: Extract Tags

For the target resource group:
- Look for any of these keys:
  - Environment: `["sec_tam_environment", "env", "environment"]`
  - EONID: `["eonid", "EONID", "inv_eon_id"]`

For the assignee:
- If it's an Enterprise App:
  - Pull `environment` and `eonid` from CSA
- If it's a Managed Identity:
  - Pull `environment` from the tags on the resource group it's deployed in

### Step 2: Validate Role

- Reject the request if the role is not in the hardcoded `APPROVED_ROLES` list

### Step 3: Validate Caller

- Caller must be an Enterprise App with CSA
- Caller `environment` must match the target RG environment tag
- Caller `eonid` must match the target RG EONID tag

### Step 4: Validate Assignee

- If Assignee is an Enterprise App:
  - Assignee `environment` must match target RG environment
  - Assignee `eonid` is **not required**
- If Assignee is a Managed Identity:
  - Its associated resource group's `environment` tag must match the target RG

---

## ❌ Failure Examples

| Caller EONID | Caller Env | Assignee Env | Target RG Env | Target RG EONID | Outcome      | Reason                                           |
|--------------|------------|--------------|----------------|------------------|--------------|--------------------------------------------------|
| 12345        | prod       | dev          | prod           | 12345            | ❌            | Assignee env mismatch                            |
| 98765        | prod       | prod         | prod           | 12345            | ❌            | Caller EONID mismatch                            |

---

## ✅ Example Success

| Caller EONID | Caller Env | Assignee Env | Target RG Env | Target RG EONID | Outcome      |
|--------------|------------|--------------|----------------|------------------|--------------|
| 12345        | prod       | prod         | prod           | 12345            | ✅ Permitted |

---

## 📌 Future Considerations

- Formalize tag normalization layer
- Real-time call to TAM to validate complex cross-env conditions
- Human access with audit logging
- Expiring access and self-revocation

---