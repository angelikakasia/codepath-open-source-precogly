# codepath-open-source-precogly

# Contribution: Fix Data Flow Asset Linking

**Contribution Number:** 2  
**Student:** Angelika S  
**Repository:** https://github.com/precogly/precogly

**Issue:** #203 https://github.com/precogly/precogly/issues/203

**Fork** https://github.com/angelikakasia/precogly   ( Last time I received zero points for my fork because CodePath didn't notice my fork but it is there - done last week)

**Status:** Phase II - 
----

## Why I Chose This Issue

I chose this issue because Precogly is an OWASP project led by my friend Vikram Narayan, whose vision is to make structured threat modeling more accessible to security professionals, developers, architects, executives, and organizations of all sizes. The platform aims to simplify the adoption of established methodologies such as STRIDE and PASTA while providing an enterprise-ready threat modeling experience.

Vikram and I also share a mentor, Marco Morana, the creator of the PASTA threat modeling methodology. Through my OWASP involvement, I have become increasingly interested in threat modeling as an important component of AI and application security. Contributing to Precogly allows me to support a project whose mission I believe in while learning from experienced members of the threat modeling community.

Issue #203 focuses on investigating why Data Assets cannot be linked to Data Flows despite the functionality being available in the user interface. Solving this issue requires understanding how the frontend, backend, and database relationships work together, making it an excellent opportunity to strengthen my backend Python and Django skills while contributing a meaningful improvement to an active OWASP open-source project.

---
## Understanding the Issue

This issue affects the relationship between Data Flows and Data Assets within Precogly's threat modeling platform.

Currently, users can create a Data Flow between two components and see a **Link Asset** option in the properties panel. However, selecting a data asset does not actually associate it with the Data Flow.

As a result, the interface exposes functionality that appears available but does not persist the relationship between the Data Flow and the selected Data Asset.

### Problem Description

Users can create a Data Flow between two components and access the **Link Asset** option within the Data Flow properties panel. However, selecting a Data Asset does not successfully associate it with the Data Flow.

Although the option is visible and clickable, no relationship is created or saved. This prevents users from accurately documenting which Data Assets travel across specific Data Flows, reducing the effectiveness of the threat model.


### Expected Behavior

Selecting a Data Asset and clicking **Link** should associate the selected Data Asset with the Data Flow. The relationship should remain visible after saving and reopening the Data Flow.
## Acceptance Criteria

- Users can successfully link an existing Data Asset to a Data Flow.
- The linked Data Asset is saved and persists after refreshing the page.
- Linked Data Assets are correctly displayed in the Data Flow properties panel.
- No existing Data Flow functionality is negatively affected.
- Automated tests are added or updated to verify the linking functionality.



### Current Behavior

After selecting an existing Data Asset and clicking **Link**, the selected asset immediately disappears from the Data Flow properties panel. No relationship is created or saved between the Data Flow and the Data Asset.



### Affected Components

Based on the local investigation, the following components appear to be involved in the Data Asset linking workflow:

- frontend/src/features/dfd-editor/components/panels/EdgeEditPanel.tsx
- frontend/src/features/dfd-editor/components/panels/NodeEditPanel.tsx
- frontend/src/features/threat-models/api/data-flow-assets.ts
- frontend/src/features/dfd-editor/DFDEditor.tsx


---

## Reproduction Process
## Environment Setup

I followed the project's Docker-based development setup using the repository README instructions.

The application was built and started successfully using Docker Compose. After the containers finished initializing and the seed data loaded, I logged in using the default development credentials provided by the project.

Environment:
- Docker Desktop
- Docker Compose
- React frontend
- Django backend


## Setup Approach

I used the Docker development environment described in the project README rather than configuring the frontend and backend manually. This ensured my local environment matched the intended development setup.

   
### Challenges Encountered

- Initial Docker image build took several minutes.
- pip displayed a warning about running as the root user inside the container. This did not prevent the application from starting.
- After the containers finished building, the seeded development account became available and the application loaded successfully.

  
### Steps to Reproduce

1. Start the local development environment using Docker Compose.
2. Log in using the seeded development account.
3. Open an existing Threat Model or create a new one.
4. Create a Human Actor, Process, Data Store, and System Actor.
5. Create Data Flows between the components.
6. Open System Context and define one or more Data Assets.
7. Open a Data Flow.
8. In the Data Assets section, select an existing Data Asset.
9. Click **Link**.
10. Observe that the selected Data Asset immediately disappears and is not associated with the Data Flow.



---
## Solution Approach

### Analysis

### Root Cause (Hypothesis)

The Link Asset user interface is present and allows selecting an existing Data Asset, but the relationship is not persisted after clicking Link.

The failure likely occurs in either:

- the React event handler responsible for submitting the selected Data Asset,
- the API request in data-flow-assets.ts,
- or the Django backend endpoint responsible for creating the Data Flow ↔ Data Asset relationship.

This hypothesis will be verified during implementation.

### Proposed Solution

Identify where the linking process fails and implement the necessary changes so that selected Data Assets are successfully associated with Data Flows and persist correctly.

### Implementation Plan
### Understand

Investigate how the Link Asset functionality is implemented by reviewing:

- EdgeEditPanel.tsx
- NodeEditPanel.tsx
- DFDEditor.tsx
- data-flow-assets.ts

Trace how a selected Data Asset is passed from the React UI to the backend API and determine where the association fails.

**Match:**

Trace the request from the user interface through the backend to identify where the relationship is lost.

**Implement:**

Update the necessary frontend or backend logic so the Data Asset relationship is correctly created and stored.

**Review:**

Verify that linked Data Assets appear correctly in the Data Flow properties and remain associated after saving and refreshing.

**Evaluate:**

Run automated and manual tests to ensure the fix works without introducing regressions.


---

## Testing Strategy

### Unit Tests





### New Automated Tests



### Integration Tests


### Manual Testing



### Week 7 Progress

- Selected Issue #203.
- Forked the repository.
- Introduced myself on the GitHub issue.
- Reviewed the issue requirements.
- Analyzed the expected and current behavior.
- Prepared an implementation plan for Phase II.

### Week 8 Progress

- Set up the project locally using Docker.
- Successfully launched the React frontend and Django backend.
- Reproduced Issue #203.
- Confirmed that linked Data Assets disappear immediately after clicking Link.
- Located the primary frontend files responsible for the Link Asset functionality.
- Began tracing the Data Flow to Data Asset relationship through the frontend API layer.



### Engineering Judgment (Stretch/Bonus)

I plan to identify the root cause of the issue by tracing how Data Flows and Data Assets are connected throughout the application. My goal is to preserve the existing architecture while making the smallest change necessary to restore the intended functionality.
  
**Next Steps**

- Identify the frontend component responsible for Link Asset.
- Trace the API request from React to the Django backend.
- Locate where the Data Flow–Data Asset relationship should be created.
- Implement the fix.
- Add tests.
- Submit a pull request.


**Files modified:**

None (Phase II focused on environment setup, reproduction, and investigation.)


  
**Branch:**

branch-203


**Repository:**

https://github.com/angelikakasia/precogly

**Key commits:**

None (implementation begins in Phase III).

  
## Issue Scope

This issue is well-scoped for a first open-source contribution. It targets a single feature, has clear reproduction steps and expected behavior, and appears to require changes within a limited portion of the codebase without affecting unrelated functionality.


**Approach decisions:**

I chose to reproduce the issue before investigating the codebase so I could confirm the bug and better understand the expected behavior before proposing a fix.



---

**PR Link:**

To be completed during Phase III.

**PR Description:**

To be completed during Phase III.






Changes:


Testing:



**Maintainer Feedback:**



---

## Learnings & Reflections

It is easier to work when the instructions are clear.

### Technical Skills Gained

Developed a deeper understanding of how Data Flows, Data Assets, and the React/Django architecture interact within Precogly. I also gained experience setting up and debugging a Docker-based open-source development environment.

### Challenges Overcome

Successfully configured the local Docker environment, reproduced the reported issue, and narrowed the investigation to the frontend components responsible for the Link Asset workflow.



### What I'd Do Differently Next Time



---
## Resources Used

- Precogly GitHub repository
- GitHub Issue #203
- Precogly CONTRIBUTING.md
- Django documentation
- Python documentation

