
#  — Software Installation Request Automation

> **Automate the end-to-end lifecycle of licensed software installation requests using ServiceNow Service Catalog** — from self-service submission to fulfillment, with zero manual coordination.

## 📌 Table of Contents

- [Business Objectives](#-business-objectives)
- [Architecture Overview](#-architecture-overview)
- [7-Phase Execution Roadmap](#-7-phase-execution-roadmap)
- [Phase 1 — Update Sets & Planning](#phase-1--update-sets--planning)
- [Phase 2 — Service Catalog & Catalog Item](#phase-2--service-catalog--catalog-item)
- [Phase 3 — Workflow Design & Flow Designer](#phase-3--workflow-design--flow-designer)
- [Phase 4 — Tables & Data Handling](#phase-4--tables--data-handling)
- [Phase 5 — Interface Design & Navigation](#phase-5--interface-design--navigation)
- [Business Rules & Automation Logic](#-business-rules--automation-logic)
- [Testing & Validation](#-testing--validation)
- [Deployment Guide](#-deployment-guide)
- [Data Integrity](#-data-integrity)
- [Innovation & Future Plans](#-innovation--future-plans)



---

## 🎯 Business Objectives

This project streamlines and automates the full lifecycle of software installation requests in ServiceNow, delivering:

| Goal | Outcome |
|---|---|
|   Reduce manual effort | Eliminated delays in request handling |
|   Faster fulfillment | Accelerated approval and installation times |
|   Standardized workflows | Dynamic approvals enforced consistently |
|   Centralized visibility | All requests tracked in one platform |
|   Better UX | Transparent, self-service experience for employees |
|   Governance & compliance | Licensing, IT, and security policies enforced |

---

##  Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                    USER (Employee)                      │
│           Submits via Service Portal / Catalog          │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
              ┌────────────────────────┐
              │  sc_cat_item           │
              │  Catalog Item          │
              │  "Software Install     │
              │   Request"             │
              └────────────┬───────────┘
                           │ creates
                           ▼
              ┌────────────────────────┐
              │  sc_request (REQ)      │
              │  Top-level Request     │
              └────────────┬───────────┘
                           │ generates
                           ▼
              ┌────────────────────────┐
              │  sc_req_item (RITM)    │
              │  Requested Item        │
              └────────────┬───────────┘
                           │ triggers
                           ▼
              ┌────────────────────────┐
              │  Flow Designer         │
              │  "install licensed     │
              │   software"            │
              └────────────┬───────────┘
                           │
            ┌──────────────┼──────────────┐
            ▼              ▼              ▼
     Ask Approval    If Approved?   If Rejected?
     on RITM         (true path)    (false path)
            │              │              │
            │              ▼              ▼
            │    Create sc_task      Notify user
            │    (SCTASK)            Close RITM
            │    → Software Team
            │    → Field Services
            ▼
    Notifications sent
    at each stage
            │
            ▼
    Request Closed / Fulfilled
```

### Table Relationships

| Table | Prefix | Role |
|---|---|---|
| `sc_request` | REQ | Top-level request record |
| `sc_req_item` | RITM | Each catalog item ordered |
| `sc_task` | SCTASK | Fulfillment task for IT teams |
| `sysapproval_approver` | — | Approval routing record |

---

## 🗺️ 7-Phase Execution Roadmap

```
Phase 1          Phase 2          Phase 3          Phase 4
Update Sets  →   Service     →   Workflow     →   Attach Flow
& Planning       Catalog          Design           to Catalog
                 Setup                             Item

Phase 5          Phase 6          Phase 7
Tables &    →   Testing &   →   Deployment &
Data             Validation       Update Set
Handling                         Migration
```

---

## Phase 1 — Update Sets & Planning

**Objective:** Capture, package, and migrate all customizations using ServiceNow Update Sets.

### Steps Followed

1. Identified scope of changes — Business Rules, Client Scripts, Workflows, Catalog Items
2. Created naming convention: `Default [InstallLicenseSoftware]`
3. Created a new Update Set in the **development PDI** (dev281572)
4. Captured all relevant changes throughout development
5. Validated alignment with ITSM governance processes

### PDI Instance Setup

![PDI Instance](img_pdi_instance.png)
*ServiceNow Personal Developer Instance (PDI) — dev281572, Zurich release, all tools installed*

---

## Phase 2 — Service Catalog & Catalog Item

**Objective:** Establish a structured, user-friendly catalog item for software installation requests.

### Service Catalog Structure

![Service Catalog](img_service_catalog.png)
*Service Catalog showing Software category under which the item is created*

### Catalog Item Configuration

- **Name:** Software Installation Request
- **Application:** InstallLicenseSoftware
- **Category:** Software
- **Catalog:** Service Catalog
- **Meta (Portal search keyword):** `New Software`
- **Status:** Active 

![Catalog Item](img_catalog_item.png)
*Catalog Item form — Software Installation Request with Application set to InstallLicenseSoftware*

### Catalog Variables (4 Variables)

| Order | Type | Question |
|---|---|---|
| 100 | Single Line Text | What software do you need? |
| 200 | Single Line Text | Specify version (if required) |
| 300 | Multi Line Text | Why do you need this software? |
| 400 | Select Box | Select urgency level (Critical / High / Normal) |

![Catalog Variables](img_catalog_variables.png)
*4 variables configured on the catalog item*

### Catalog Item — Try It Preview

![Catalog Form](img_catalog_form.png)
*Live catalog item form with urgency dropdown showing Critical, High, Normal options*

---

## Phase 3 — Workflow Design & Flow Designer

**Objective:** Automate approvals, task creation, and notifications using Flow Designer.

### Flow: `install licensed software`

- **Trigger:** Service Catalog
- **Application:** InstallLicenseSoftware
- **Status:** Active / Published 

### Workflow Editor

![Workflow Editor](img_workflow_editor.png)
*Workflow Studio — entry point for creating the automated flow*

### Flow Designer — Visual View

![Flow Designer](img_flow_designer.png)
*Flow Designer showing the 3-step automation: Service Catalog Trigger → Approval → Conditional Task Creation*

### Flow Designer — List View

![Flow Designer List](img_flow_designer2.png)
*Detailed list view: Trigger → Step 1 (Ask For Approval) → Step 2 (If Approved?) → Step 3 (Create Catalog Task)*

### Flow Steps

| Step      | Action | Details |
|-----------|---|---|
| Trigger   | Service Catalog | Fires when RITM is created |
| 1         | Ask For Approval on Requested Item | Routes to Manager / IT Admin |
| 2         | If (Approval State is Approved)    | Conditional branch — true / false |
| 3 (true)  | Create Catalog Task Record         | Assigns SCTASK to Software Support Team |
| 3 (false) | Error Handler / Rejection path     | Notifies requester of rejection |

### Attach Flow to Catalog Item (Process Engine)

![Process Engine](img_process_engine.png)
*Process Engine tab — Flow "install licensed software" attached to the catalog item*

**Steps to attach:**
1. Open the Catalog Item → go to **Process Engine** tab
2. In the **Flow** field, select `install licensed software`
3. Leave Workflow field empty (only one engine at a time)
4. Click **Update**

---

## Phase 4 — Tables & Data Handling

**Objective:** Understand and validate how data flows across ServiceNow tables.

### sc_req_item Table (RITM)

![RITM List](img_ritm_list.png)
*RITM list showing multiple approved Software Installation Requests (RITM0010001–RITM0010004)*

### RITM Detail Record

![RITM Detail](img_ritm_detail.png)
*RITM0010004 — showing variables auto-filled: Software = Chrome, Version = 1.6, Reason = Test*

### RITM — Catalog Tasks Tab

![RITM Tasks](img_ritm_tasks.png)
*RITM0010004 showing 2 auto-created catalog tasks: SCTASK (Software group) + SCTASK (Field Services)*

### sc_task Table (SCTASK)

![SCTASK List](img_sctask_list.png)
*Catalog Tasks list — SCTASKs auto-assigned to Software and Field Services groups*

### SCTASK Detail Record

![SCTASK Detail](img_sctask_detail.png)
*SCTASK0010007 — Assess or Scope Task, assigned to Field Services, linked to RITM0010004*

---

## Phase 5 — Interface Design & Navigation

**Objective:** Provide a clean, user-friendly self-service experience via the Service Portal.

### Service Portal Navigation

1. Login to your ServiceNow PDI
2. Copy instance URL: `https://dev281572.service-now.com`
3. Open a new tab and add `/sp` to the URL:
   ```
   https://dev281572.service-now.com/sp
   ```
4. Search for **"New Software"** in the portal search bar
5. Open **Software Installation Request**
6. Fill in the form fields and click **Order Now**
7. Confirm in the **Order Confirmation** modal → click **Checkout**
8. Note your **REQ number** and await email notification

### Portal Search

![Portal Search](img_portal_search.png)
*Searching "New Software" in Service Portal — meta keyword routes to the catalog item*

### Request Form (Service Portal)

![Request Form](img_request_form.png)
*Software Installation Request form filled with: Software = "licensed one", Version = 1.1, Reason = "good UI", Urgency = Critical*

### Order Confirmation Modal

![Order Confirmation](img_order_confirmation.png)
*Order Confirmation dialog — Request for System Administrator, optional delivery info, Checkout button*

### Order Submitted Successfully

![Order Status](img_order_status.png)
*Order Status — REQ0010004 submitted, Estimated Delivery 2026-03-08, stage tracker visible*

### Request Summary

![Request Summary](img_request_summary.png)
*Request Summary — REQ0010003 at "Assess or Scope Task" stage*

### Usability Features

-   **Order number** displayed immediately after submission
-   **Delivery time** shown as 2 Days
-   **Stage tracker** visible on order status
-   **Tooltip/Help Text** icons added next to license-related fields
-   **Urgency dropdown** — Critical, High, Normal options

---

##  Business Rules & Automation Logic

### Business Rule: `Create Incident in license issue`

- **Table:** `sc_req_item` (Requested Item)
- **Application:** InstallLicenseSoftware
- **When:** After — Insert & Update
- **Condition:** `current.state.changesTo('On Hold')`

![Business Rule Config](img_business_rule_config.png)
*Business Rule configuration — runs After Insert/Update on Requested Item table*

![Business Rule Script](img_business_rule_script.png)
*Business Rule script — auto-creates an Incident when RITM goes On Hold due to license unavailability*

### Script Logic

```javascript
(function executeRule(current, previous /*null when async*/) {

    if (current.state == 3) { // On Hold

        var inc = new GlideRecord('incident');
        inc.initialize();
        inc.short_description = 'License unavailable for ' + current.request_item;
        inc.description = 'Software request could not be fulfilled due to license shortage.';
        inc.caller_id = current.request.requested_for;
        inc.assignment_group.setDisplayValue('Software Support');
        inc.priority = 3;
        inc.insert();
    }

})(current, previous);
```

### What This Achieves

- Automatically creates an Incident when a software request is put **On Hold** due to license shortage
- Routes the incident to the **Software Support** group
- Sets caller to the original requester for full traceability
- Maintains compliance and audit trail

---

##  Testing & Validation

### Test Checklist

- [x] Submitted test Software Installation Request via Service Portal
- [x] Confirmed request generates REQ → RITM automatically
- [x] Approval routing works (approval shown as Approved on RITM list)
- [x] Catalog tasks (SCTASK) auto-created and assigned to correct groups
- [x] RITM variables correctly populated (Software Name, Version, Justification)
- [x] Stage tracker visible in portal after submission
- [x] Business Rule fires correctly when RITM goes On Hold
- [x] Update Set exported to XML successfully
- [x] Update Set imported to second instance successfully
- [x] Update Set committed — **Succeeded in 0 Seconds** 
- [x] All data validated in target instance — no missing elements

### Update Sets — Complete

![Update Sets](img_update_sets.png)
*Update Sets list — Default [InstallLicenseSoftware] marked as Complete*

### Update Set Commit — Success

![Update Set Commit](img_update_set_commit.png)
*Update Set committed successfully — "Succeeded in 0 Seconds", 100% progress*

---

##  Deployment Guide

### Step-by-Step Deployment

**Step 1 — Complete the Update Set (Dev Instance)**
```
System Update Sets > Local Update Sets
→ Open "Default [InstallLicenseSoftware]"
→ Change State: In Progress → Complete
→ Click Update
→ Click Export to XML → Download file
```

**Step 2 — Import to Target Instance**
```
System Update Sets > Retrieved Update Sets
→ Click "Import Update Set from XML"
→ Upload the downloaded .xml file
→ Open the new record
```

**Step 3 — Preview & Commit**
```
→ Click "Preview Update Set"
→ Resolve any conflicts if shown
→ Click "Commit Update Set"
→ Confirm: "Update set committed - Succeeded"
```

**Step 4 — Validate in Target**
```
→ Open Service Portal (/sp)
→ Search "New Software"
→ Submit a test request
→ Verify REQ → RITM → SCTASK chain
→ Confirm approval flow and notifications
```

##  Data Integrity

### Client-Side Validation (UI Policies)
- Mandatory fields enforced: Software Name, Version, Business Justification
- Dynamic field visibility — extra fields shown only when "Licensed Software" selected
- Prevents incomplete submissions before reaching the server

### Server-Side Validation (Business Rules)
- Validates data integrity on insert/update
- Auto-assigns tasks to the correct IT fulfillment groups
- Creates Incidents automatically on license issues

### Auto-Population
- **Requested For** — defaults to logged-in user, editable for proxy requests
- **Department & Location** — pulled dynamically from user profile
- **RITM Variables** — captured from catalog form and stored with backend field names

### Table-Level Integrity

| Layer          | Table                           | Validation Applied                                  
------------------------------------------------------------------------------------------------
| Request -->       `sc_request`     -->      | Created automatically on checkout                   
| Requested Item --> `sc_req_item`   --> Variables bound, mandatory fields enforced          
| Catalog Task   --> `sc_task`    --> Auto-created on approval, assigned to correct group 

| Approval       --> `sysapproval_approver` --> Routed based on flow logic                       


##  Innovation & Future Plans

- **End-to-End Automation** — No manual handoffs from submission to task assignment
- **Dynamic Approval Routing** — Software Asset Team auto-notified for licensed software requests
- **License Issue Handling** — Auto-creates Incident if software goes On Hold (unique business rule)
- **Scalable Architecture** — Built using out-of-the-box Flow Designer for easy maintenance
- **Clean UX** — UI Policies show/hide fields dynamically, keeping the form uncluttered

### Future Enhancements

-  **Integration Hub** — Connect to software deployment tools (SCCM, Ansible, Jamf)
-  **Auto-provisioning** — Push software installations automatically on task completion
-  **Reporting Dashboards** — Track request volumes, fulfillment rates, license usage
-  **Expansion** — Extend to device provisioning, access requests, VPN setup
-  **Virtual Agent** — Add chatbot intake for software requests via Teams/Slac

