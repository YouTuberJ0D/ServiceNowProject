# ServiceNow Software Installation Request Automation

## Project Overview
This project automates licensed software installation requests using ServiceNow Service Catalog.

## Features Implemented
- Service Catalog Item for Software Request
- Approval Workflow
- Automated Task Creation
- Status Tracking
- License Compliance Handling

## Technologies Used
- ServiceNow
- Workflows
- Business Rules
- Service Catalog

# Phase 1 : Requirement Analysis and Planning
-Business objectives : The business objective for Software Installation Request Automation in ServiceNow aligns with improving efficiency, reducing manual coordination, and enhancing user satisfaction in managing software-related requests.
To streamline and automate the end-to-end lifecycle of software installation requests using ServiceNow, thereby:
--Reducing manual effort and eliminating delays in request handling
--Accelerating approval and fulfillment times for software installations
--Enforcing standardized workflows and dynamic approval processes
--Improving visibility and tracking of requests through a centralized platform
--Enhancing end-user experience with faster, transparent service delivery
--Ensuring compliance with licensing, IT governance, and security policies

-Functional Scope : The functional scope of this project covers the automation of software installation requests in ServiceNow
- SkateHolder Mapping
- Execution RoadMap : 
--Update Sets
Description
Objective: The objective of using Update Sets in ServiceNow is to capture, package, and migrate customizations or configurations.
Identify the scope of changes (Business Rules, Client Scripts, UI Actions, Workflows, etc.).
Define a naming convention and documentation standards for update sets.
Create a new Update Set in the development environment.
Collaborate with developers and administrators to ensure all relevant changes are captured.
Validate that the Update Set aligns with deployment and ITSM governance processes.
--Service Catalog
Description
Objective: Establish a structured, user-friendly service catalog for IT and business requests.
Identify service request types (e.g., hardware, software, access, facilities).
Define catalog categories (Hardware, Software, Network, HR, Facilities, etc.).
Create service catalog items with clear titles and detailed descriptions.
Collaborate with respective business and IT teams for content accuracy.
Ensure catalog taxonomy aligns with ITSM processes and organizational needs.
--Workflow Design & Planning
Description
Objective: Define a clear structure for automating processes using workflows.
Identify the business process to be automated (e.g., incident escalation, change approval, service catalog fulfillment).
Document process steps, decision points, and dependencies.
Define roles, responsibilities, and stakeholders for each step.
Ensure the workflow aligns with ITIL and ITSM best practices.
Validate workflow requirements with business and IT teams.
--Attach Workflow to Catalog Item
Description
Objective: Link the workflow to the specific catalog item.
Open the Service Catalog item in ServiceNow Studio / Form view.
Navigate to the Workflow field on the catalog item form.
Select the created workflow from the list.
Save and publish the catalog item.
Ensure only one active workflow is attached (to avoid conflicts).
--Requirement Analysis & Table Identification
Description
Objective: Define which tables will be used to capture and manage catalog item requests.
Define which tables will be used to capture and manage catalog item requests.
Identify the Request-related tables in ServiceNow:
sc_request → Request (REQ)
sc_req_item → Requested Item (RITM)
sc_task → Catalog Task (SCTASK)
Determine the relationships (REQ → RITM → SCTASK).
Map business requirements to table data fields (who requests, what items, approval flow, fulfillment tasks).
Collaborate with stakeholders to finalize data handling requirements.
--Tables Handling
Description
Objective: Define which tables will be used to capture and manage catalog item requests.
Define which tables will be used to capture and manage catalog item requests.
Identify the Request-related tables in ServiceNow:
sc_request → Request (REQ)
sc_req_item → Requested Item (RITM)
sc_task → Catalog Task (SCTASK)
Determine the relationships (REQ → RITM → SCTASK).
Map business requirements to table data fields (who requests, what items, approval flow, fulfillment tasks).
Collaborate with stakeholders to finalize data handling requirements.
--Testing & Validation
Description
Objective: Ensure the Software Installation Request process works seamlessly from request submission to fulfillment.
Submit a test Software Installation Request via the Service Portal.
Confirm rejection/approval behaviors are handled properly.
Check that catalog tasks are auto-assigned to the correct IT support/Software team.
Ensure requesters receive notifications (submission, approval, completion).
Ensure business rules prevent incomplete requests.
--Deployment
Description
Objective: Deploy the automation into production and optimize over time.
Capture all configurations in an Update Set.
Migrate from Development → Test → Production.
Validate functionality in production with live requests.
Train end-users and IT support teams.
Document workflow, approvals, and escalation paths.
