**Functional Specification Document**

**Project:** CaseMaster 365
**Version:** 1.0.0.0
**Date:** May 17, 2025
**Author:** \[Your Organization]

**Project Description:** CaseMaster 365 is a lightweight, customizable case management solution built on Dynamics 365 CRM to help businesses track, prioritize, and resolve customer issues efficiently.

---

## 1. Document Purpose

This Functional Specification Document (FSD) describes the features, functional requirements, user roles, data model, and high-level architecture for a generic Case Management System implemented on Dynamics 365 CRM. It is intended for prospective customers evaluating the solution.

## 2. Scope

* **In Scope:**

  * Custom Case Types, fields, forms, and views
  * SLA definition, tracking and escalations
  * Attachment management (upload, versioning)
  * Role-based security and permissions
  * Notifications via email/Teams
  * Reporting and dashboards for SLA compliance and workload

* **Out of Scope:**

  * Integration with external ERP systems (future phase)
  * Advanced AI/ML analytics (future phase)

## 3. Stakeholders

* **Business Sponsor:** Head of Customer Support
* **Product Owner:** CRM Solutions Manager
* **End Users:** Support Agents, Managers, Customers (portal)
* **Technical Team:** CRM Developers, System Administrators, QA

## 4. Business Requirements

1. Enable any business to log and manage customer issues (“cases”)
2. Ensure SLAs are defined per case type and automatically tracked
3. Allow attachments (documents, images) to be stored against cases
4. Provide role-based access: Requesters create cases; Agents work cases; Managers oversee SLAs
5. Offer dashboards to monitor open cases, SLA breaches, team performance

## 5. Functional Requirements

### 5.1 Case Management

* **FR1:** Create Case record with fields: Case ID, Title, Description, Case Type, Category, Subcategory, Priority, Status, Owner, Submission Date
* **FR2:** Edit/update case fields and change status (New, In Progress, Resolved, Closed)
* **FR3:** Associate custom fields per case type (e.g., Product Code, Location)
* **FR3a:** Classify each case by **Category** and **Subcategory** to enable finer grouping and reporting
* **FR4:** Provide quick-create form and full form in Dynamics UI

### 5.2 SLA Engine

* **FR5:** Define SLA templates: Response Time (hours), Resolution Time (hours) per Case Type
* **FR6:** Automatically generate SLA Instance on case creation with due timestamps
* **FR7:** Monitor SLA status; update SLA Instance fields (On Time, Breached)
* **FR8:** Trigger escalations via email/Teams when SLA thresholds approach or breach

### 5.3 Attachment Manager

* **FR9:** Enable file upload (PDF, images, Office docs) via CRM form
* **FR10:** Store attachments in Azure Blob Storage; link URI in CRM
* **FR11:** Maintain version history: track Uploaded Date, Uploaded By, Version Number
* **FR12:** Display attachments list in case form with download/view actions

### 5.4 Notifications

* **FR13:** Send email/Teams notification on case assignment, status change, SLA breach
* **FR14:** Templates configurable via Power Automate flows

### 5.5 Security & Roles

* **FR15:** Define Security Roles: Requester, Agent, Manager
* **FR16:** Permissions:

  * Requester: Create/view own cases
  * Agent: View/create/update assigned cases
  * Manager: View/update all cases, override ownership, manage SLAs

### 5.6 Reporting & Dashboard

* **FR17:** Out-of-the-box Power BI Dashboard embedded in CRM:

  * Open cases by status/agent
  * SLA compliance rate
  * Cases created per day/week/month
* **FR18:** Exportable to Excel

### 5.7 Business Process Flow (BPF)

* **FR19:** Define a BPF for cases with stages: **New**, **Investigate**, **Resolution**, **Closed**
* **FR20:** At each stage transition, require execution of stage-specific activities (e.g., on Investigate enter—assign agent; on Resolution enter—propose solution)
* **FR21:** Record each stage change as a Post (Timeline Activity) against the case, capturing Old Stage, New Stage, Changed By, and Timestamp
* **FR22:** Trigger in-app notifications to involved users (Requester, Owner, Managers) on every stage transition via CRM native notification framework

## 6. Use Cases Use Cases

| ID  | Title                   | Actor       | Description                                                                      |
| --- | ----------------------- | ----------- | -------------------------------------------------------------------------------- |
| UC1 | Submit New Case         | Requester   | User fills quick-create form; case record created; SLA Instance generated.       |
| UC2 | Acknowledge Case        | Agent       | Agent opens assigned case; updates status to In Progress; adds notes/attachments |
| UC3 | SLA Breach Escalation   | System      | SLA Engine detects breach; sends notification to Manager and Agent               |
| UC4 | Attach Document to Case | Agent       | Agent uploads a file; AttachmentService stores file; link shown in case form     |
| UC5 | View SLA Dashboard      | Manager     | Manager opens dashboard to review SLA metrics and drill into breach details      |
| UC6 | Case Stage Transition   | System/User | On BPF stage change: record timeline post and send in-app notifications          |


## 7. Data Model (Entities)

* **Case** (custom entity)

  * CaseId (GUID), Title, Description, CaseType (choice), **Category** (choice), **Subcategory** (choice), Priority, Status, Owner (User), CreatedOn, ModifiedOn
* **SLA Definition**\*\* (custom entity)

  * SLADefinitionId, CaseType, ResponseHours, ResolutionHours, EscalationTemplate
* **SLA Instance** (custom entity)

  * SLAInstanceId, Case (lookup), SLADefinition (lookup), ResponseDue, ResolutionDue, Status
* **Attachment Meta** (custom entity)

  * AttachmentId, Case (lookup), FileName, BlobUri, Version, UploadedBy, UploadedOn
* **SystemUser** (out-of-the-box)

## 8. High-Level Architecture

```plaintext
[ Dynamics 365 CRM UI ]
    ↕
[ Plugins & Workflows ] → [ Power Automate Flows ] → [ Azure Blob Storage ]
    ↕
[ Dataverse (Databases & Tables) ]
```

* **Custom Plugins:** Enforce SLA instance creation on CaseCreate
* **Power Automate:** Handle notifications and attachment file transfer
* **Azure Blob Storage:** Store binary files; maintain access via secure URI

## 9. Non-Functional Requirements

* **Performance:** ≤1 second page load for case form with ≤50 attachments
* **Availability:** 99.9% uptime SLA
* **Scalability:** Support 10,000 active cases and 500 concurrent users
* **Security:** Data encryption at rest and in transit; Azure AD authentication

## 10. Deployment & Licensing

* Delivered as a Managed Solution (versioned)
* Compatible with Dynamics 365 Customer Service license P2 or above
* Deployment via Package Deployer or Admin Center import

## 11. Glossary

* **SLA:** Service-Level Agreement
* **Dataverse:** Underlying data platform for Dynamics CRM
* **Plugin:** .NET assembly executing server-side business logic

---
