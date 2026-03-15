# AZ-104 Lab: Azure Identities and Governance
**Session 1 — Hands-On Practice**

---

## Prerequisites

- An active Azure subscription (Free tier or Pay-As-You-Go)
- Access to the [Azure Portal](https://portal.azure.com)
- A user account with Owner or Global Administrator role

---

## Lab Overview

In this lab you will practice the core skills covered in Session 1:

1. Create and manage users and groups in Microsoft Entra ID
2. Assign RBAC roles to control access
3. Create a Resource Group and apply Tags
4. Create and assign an Azure Policy
5. Apply a Resource Lock
6. Set up a Cost Management Budget alert

Estimated time: **60–90 minutes**

---

## Exercise 1 — Create a User in Microsoft Entra ID

### Steps

1. Sign in to the [Azure Portal](https://portal.azure.com).
2. In the search bar, type **Microsoft Entra ID** and select it.
3. In the left menu, select **Users**, then click **+ New user > Create new user**.
4. Fill in the following details:

   | Field | Value |
   |---|---|
   | User principal name | `labuser01@<your-domain>.onmicrosoft.com` |
   | Display name | `Lab User 01` |
   | Password | Auto-generate (copy it) |

5. Click **Review + create**, then **Create**.

### Verify

- Confirm the user appears in the **Users** list.
- Note the UPN for use in later exercises.

---

## Exercise 2 — Create a Security Group and Add the User

### Steps

1. In **Microsoft Entra ID**, select **Groups** from the left menu.
2. Click **+ New group**.
3. Set the following:

   | Field | Value |
   |---|---|
   | Group type | Security |
   | Group name | `AZ104-Lab-Group` |
   | Membership type | Assigned |

4. Under **Members**, click **No members selected** and add `Lab User 01`.
5. Click **Create**.

### Verify

- Open the group and confirm the member is listed under **Members**.

---

## Exercise 3 — Create a Resource Group and Apply Tags

### Steps

1. In the search bar, type **Resource groups** and select it.
2. Click **+ Create**.
3. Fill in:

   | Field | Value |
   |---|---|
   | Subscription | Your subscription |
   | Resource group name | `rg-az104-lab` |
   | Region | East US (or your preferred region) |

4. Click **Next: Tags**.
5. Add the following tags:

   | Name | Value |
   |---|---|
   | Environment | Lab |
   | Owner | YourName |
   | CostCenter | Training |

6. Click **Review + create**, then **Create**.

### Verify

- Open the resource group and confirm all three tags are visible under **Tags**.

---

## Exercise 4 — Assign an RBAC Role

### Steps

1. Open the `rg-az104-lab` resource group.
2. In the left menu, click **Access control (IAM)**.
3. Click **+ Add > Add role assignment**.
4. On the **Role** tab, select **Reader**, then click **Next**.
5. On the **Members** tab, click **+ Select members**.
6. Search for and select `Lab User 01`, then click **Select**.
7. Click **Review + assign** twice.

### Verify

- On the **Role assignments** tab, confirm `Lab User 01` appears with the **Reader** role.

---

## Exercise 5 — Create and Assign an Azure Policy

### Steps

1. In the search bar, type **Policy** and select it.
2. Select **Assignments** in the left menu, then click **+ Assign policy**.
3. Click the **Policy definition** field and search for:
   `Require a tag on resource groups`
4. Select the policy and click **Add**.
5. Set the scope to your subscription or `rg-az104-lab`.
6. Under **Parameters**, set the **Tag Name** to `Environment`.
7. Click **Review + create**, then **Create**.

### Verify

- Wait 1–2 minutes, then try creating a new resource group **without** the `Environment` tag.
- The portal should show a policy compliance warning.

---

## Exercise 6 — Apply a Resource Lock

### Steps

1. Open the `rg-az104-lab` resource group.
2. In the left menu, click **Locks**.
3. Click **+ Add**.
4. Fill in:

   | Field | Value |
   |---|---|
   | Lock name | `lock-no-delete` |
   | Lock type | Delete |
   | Notes | Prevents accidental deletion |

5. Click **OK**.

### Verify

- Attempt to delete the resource group (portal will show an error blocking deletion).
- Remove the lock when testing is complete.

---

## Exercise 7 — Set a Cost Management Budget Alert

### Steps

1. In the search bar, type **Cost Management** and select it.
2. Select **Budgets** from the left menu, then click **+ Add**.
3. Configure the budget:

   | Field | Value |
   |---|---|
   | Name | `lab-budget` |
   | Reset period | Monthly |
   | Budget amount | $10 |

4. Click **Next: Alerts**.
5. Add an alert at **80%** threshold and enter your email address.
6. Click **Create**.

### Verify

- The budget appears in the **Budgets** list with status **Active**.

---

## Clean Up

To avoid unwanted charges, remove the lab resources when done:

1. Delete the `rg-az104-lab` resource group (remove the lock first).
2. Delete `Lab User 01` from Microsoft Entra ID.
3. Delete the `AZ104-Lab-Group` group.
4. Remove the Azure Policy assignment.
5. Delete the Cost Management budget.

---

