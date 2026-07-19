# codepath-open-source-precogly

# Contribution: Fix Data Flow Asset Linking

**Contribution Number:** 2  
**Student:** Angelika S  
**Repository:** https://github.com/precogly/precogly

**Issue:** #203 https://github.com/precogly/precogly/issues/203

**Status:** Phase I - Issue Selected

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


## Expected Behavior

When a user clicks **Link Asset**, they should be able to select an existing Data Asset and successfully associate it with the selected Data Flow.

The linked asset should be saved and displayed within the Data Flow properties.

## Acceptance Criteria

- Users can successfully link an existing Data Asset to a Data Flow.
- The linked Data Asset is saved and persists after refreshing the page.
- Linked Data Assets are correctly displayed in the Data Flow properties panel.
- No existing Data Flow functionality is negatively affected.
- Automated tests are added or updated to verify the linking functionality.



## Current Behavior

The **Link Asset** option is available within the Data Flow properties panel, but selecting a Data Asset does not create or save the relationship.

The operation appears to complete without errors, yet the selected Data Asset is never attached to the Data Flow and does not appear in the interface.


### Affected Components
- Data Flow properties panel
- Data Asset linking functionality
- Backend relationship between Data Flows and Data Assets
- API or service responsible for saving Data Flow relationships
---

## Reproduction Process
## Environment Setup

To be completed during Phase II after setting up the local development environment.


## Setup Approach

Planned for Phase II. I will first reproduce the issue locally before tracing the Data Asset linking functionality through the frontend and backend.


   

### Challenges Encountered

At this stage, no implementation challenges have been encountered. The primary focus of Phase I is understanding the issue, reviewing the project structure, and planning the investigation for Phase II.


## Steps to Reproduce

According to the issue report:

1. Create two components.
2. Create a Data Flow between them.
3. Open the Data Flow properties panel.
4. Navigate to the Data Assets section.
5. Click **Link Asset**.
6. Attempt to link an existing Data Asset.
7. Observe that the Data Asset is not associated with the Data Flow.



---
## Solution Approach

### Analysis

**Root Cause:**

Investigate how Data Assets are associated with Data Flows by reviewing the frontend request, backend API, database models, and service logic responsible for creating the relationship.

### Proposed Solution

Identify where the linking process fails and implement the necessary changes so that selected Data Assets are successfully associated with Data Flows and persist correctly.

### Implementation Plan

**Understand:**

Review the existing implementation for Data Flows, Data Assets, and the Link Asset functionality.

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




### Engineering Judgment (Stretch/Bonus)

I plan to identify the root cause of the issue by tracing how Data Flows and Data Assets are connected throughout the application. My goal is to preserve the existing architecture while making the smallest change necessary to restore the intended functionality.
  
**Next Steps:**

- Set up the local development environment.
- Reproduce the issue.
- Identify the root cause.
- Implement the fix.
- Add automated tests.
- Submit a pull request.



**Files modified:**


  
**Branch:**



**Repository:**


**Key commits:**


  
## Issue Scope

This issue is well-scoped for a first open-source contribution. It targets a single feature, has clear reproduction steps and expected behavior, and appears to require changes within a limited portion of the codebase without affecting unrelated functionality.



**Approach decisions:**



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



### Technical Skills Gained

Developed a deeper understanding of the problem domain and how Data Flows and Data Assets are expected to interact within a threat modeling platform.

### Challenges Overcome

Interpreted the issue requirements and translated them into a structured implementation plan.




### What I'd Do Differently Next Time



---
## Resources Used

- Precogly GitHub repository
- GitHub Issue #203
- Precogly CONTRIBUTING.md
- Django documentation
- Python documentation

