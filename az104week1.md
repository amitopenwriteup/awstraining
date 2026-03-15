Here are **AZ-104 Session 1 interview questions** in markdown format:

---

# AZ-104 Interview Questions
## Session 1: Identities & Governance

---

##  Microsoft Entra ID (Azure AD)

**Q1. What is Microsoft Entra ID and how does it differ from on-premises Active Directory?**
> Microsoft Entra ID is a cloud-based identity and access management service. Unlike on-premises AD (which uses LDAP, Kerberos, and manages domain-joined machines), Entra ID is designed for cloud/SaaS apps using protocols like OAuth 2.0, OIDC, and SAML. It doesn't support Group Policy Objects (GPOs) or traditional OU structures.

---

**Q2. What is the difference between a Member User and a Guest User in Entra ID?**
> - **Member User**: Internal user created within the tenant — has full access to directory resources by default.
> - **Guest User**: External identity invited via B2B collaboration — limited directory access, used for partner/vendor collaboration.

---

**Q3. What is Conditional Access and when would you use it?**
> Conditional Access is a policy engine in Entra ID that enforces access decisions based on conditions such as user location, device compliance, risk level, and app being accessed. Example: Block access to the Azure portal if the login comes from outside India or from a non-compliant device.

---

**Q4. What is SSPR and what are its benefits?**
> Self-Service Password Reset (SSPR) allows users to reset their own passwords without IT intervention. Benefits include reduced helpdesk load, faster resolution time, and improved user productivity. It can require verification via email, phone, or authenticator app.

---

**Q5. What is Multi-Factor Authentication (MFA) and what verification methods does it support?**
> MFA requires users to prove identity using two or more factors: something they **know** (password), something they **have** (authenticator app, phone), or something they **are** (biometrics). Supported methods in Entra ID include Microsoft Authenticator, SMS, voice call, hardware FIDO2 keys, and software OATH tokens.

---

##  Users, Groups & Devices

**Q6. What are the types of groups in Entra ID?**
> - **Security Groups**: Used to assign permissions and access to resources.
> - **Microsoft 365 Groups**: Used for collaboration (shared mailbox, Teams, SharePoint).
> - **Dynamic Groups**: Membership is automatically managed based on user/device attributes (e.g., `department eq "Finance"`).
> - **Assigned Groups**: Membership is managed manually.

---

**Q7. What is the difference between Azure AD Join and Hybrid Azure AD Join?**

| | Azure AD Join | Hybrid Azure AD Join |
|---|---|---|
| **Device location** | Cloud-only | On-prem + Cloud |
| **OS support** | Windows 10/11 | Windows 7, 8.1, 10, 11 |
| **Use case** | New/cloud-first devices | Existing domain-joined devices |
| **GPO support** | ❌ No | ✅ Yes |

---

##  Role-Based Access Control (RBAC)

**Q8. What are the three built-in RBAC roles and what can each do?**
> - **Owner**: Full access + can manage role assignments
> - **Contributor**: Full access to create/manage resources, but **cannot** manage role assignments
> - **Reader**: View-only access — cannot make any changes

---

**Q9. How does RBAC scope work in Azure?**
> RBAC can be assigned at four levels, each inheriting down:
> ```
> Management Group → Subscription → Resource Group → Resource
> ```
> A role assigned at the Subscription level applies to all Resource Groups and Resources within it.

---

**Q10. What is the difference between RBAC and Azure AD roles?**
> - **RBAC** controls access to **Azure resources** (VMs, storage, databases, etc.)
> - **Azure AD roles** control access to **Entra ID objects** (users, groups, app registrations)
> - Example: *Global Administrator* is an AD role; *Contributor* is an RBAC role.

---

**Q11. Can a user have multiple role assignments? How are permissions resolved?**
> Yes. Azure RBAC uses an **additive model** — all role assignments are combined. However, **deny assignments take precedence** over any allow. If a user is Reader at subscription level and Contributor at a resource group, they have Contributor-level access within that RG.

---

##  Azure Policy

**Q12. What is Azure Policy and what problems does it solve?**
> Azure Policy is a governance tool that enforces organizational rules on Azure resources. It solves problems like unauthorized region deployments, missing mandatory tags, use of unapproved SKUs, and non-compliant resource configurations. It can **audit**, **deny**, or **auto-remediate** resources.

---

**Q13. What are the Policy effect types? List at least four.**
> | Effect | Behavior |
> |--------|----------|
> | `Deny` | Blocks resource creation/update if non-compliant |
> | `Audit` | Allows resource but flags it as non-compliant |
> | `Append` | Adds fields to the resource (e.g., tags) |
> | `Modify` | Adds/updates tags or properties on resources |
> | `DeployIfNotExists` | Deploys a related resource if it doesn't exist |
> | `AuditIfNotExists` | Audits if a related resource is missing |

---

**Q14. What is the difference between a Policy and a Policy Initiative (Policy Set)?**
> - **Policy**: A single rule (e.g., "Require CostCenter tag")
> - **Initiative (Policy Set)**: A collection of related policies grouped together for a common goal (e.g., "Enable Azure Security Center monitoring" contains 20+ policies)

---

##  Resource Locks

**Q15. What are the two types of Resource Locks and how do they differ?**
> | Lock Type | Can Modify? | Can Delete? |
> |-----------|-------------|-------------|
> | `CanNotDelete` | ✅ Yes | ❌ No |
> | `ReadOnly` | ❌ No | ❌ No |

---

**Q16. Can an Owner override a Resource Lock?**
> No. Resource Locks override RBAC permissions — **even Owners and Contributors cannot delete or modify a locked resource**. To make changes, the lock must first be explicitly removed by someone with the `Microsoft.Authorization/locks/delete` permission.

---

**Q17. At what scopes can Resource Locks be applied?**
> Locks can be applied at **Subscription**, **Resource Group**, or individual **Resource** level. A lock on a Resource Group automatically applies to **all resources within it**.

---

## 🏢 Governance: Subscriptions & Management Groups

**Q18. What is the hierarchy of Azure resource organization from top to bottom?**
> ```
> Azure AD Tenant
>     └── Root Management Group
>             └── Child Management Groups
>                     └── Subscriptions
>                             └── Resource Groups
>                                     └── Resources
> ```

---

**Q19. What is the purpose of Management Groups?**
> Management Groups allow you to apply **RBAC and Azure Policy** across multiple subscriptions at once. Policies and roles assigned at a Management Group level are inherited by all subscriptions and resources below it. Useful for large enterprises with multiple teams and subscriptions.

---

**Q20. What is the difference between a Subscription and a Resource Group?**
> - **Subscription**: Billing boundary + access control boundary. Resources across RGs in the same sub share the same billing account.
> - **Resource Group**: Logical container for grouping related resources. Used for lifecycle management — deleting an RG deletes all resources inside it.

---

## Tags & Cost Management

**Q21. What are Azure Tags and what are their limitations?**
> Tags are **key-value pairs** applied to resources for organization, cost tracking, and automation. Limitations:
> - Max **50 tags** per resource
> - Tag **names** are case-insensitive; tag **values** are case-sensitive
> - Not all resource types support tags
> - Tags are **not inherited** by child resources automatically

---

**Q22. How would you enforce mandatory tagging across all resources in an organization?**
> Use **Azure Policy** with the `Deny` effect: deny resource creation if a required tag (e.g., `CostCenter`) is missing. Alternatively, use the `Modify` effect to **automatically append** the tag to resources that don't have it. Assign the policy at the **Management Group** level for organization-wide enforcement.

---

**Q23. What tools does Azure provide for cost monitoring and control?**
> - **Azure Cost Management + Billing**: Dashboards, cost analysis, budgets, and alerts
> - **Budgets**: Set spending thresholds with email/action group alerts
> - **Cost Allocation**: Split shared costs across departments using tags
> - **Advisor Recommendations**: Right-size or shut down underutilized resources
> - **Export**: Schedule cost data exports to a storage account for Power BI reporting

---

##  Scenario-Based Questions

**Q24. A junior admin accidentally deleted a production database. How could this have been prevented?**
> Apply a **`CanNotDelete` Resource Lock** on the database resource or its Resource Group. This would have blocked the deletion regardless of the admin's RBAC role. Combined with RBAC least privilege (assigning only the minimum required role), the risk is minimized.

---

**Q25. Your organization must ensure Azure resources are deployed only in India regions. How do you enforce this?**
> Create an **Azure Policy** using the `Allowed Locations` built-in policy definition, set to `Central India` and `South India`. Assign it at the **Root Management Group** so it applies to all subscriptions. Set the effect to `Deny` to block non-compliant deployments.

---
