# codepath-open-source-precogly

# Contribution: Fix Data Flow Asset Linking

**Contribution Number:** 2  
**Student:** Angelika S  
**Repository:** Precogly  
**Issue:** #203 - Data Flow: Cannot Link Data Assets to a Connection  
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
### Environment Setup



  
### Setup Approach


   

### Challenges Encountered




### Steps to Reproduce




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




### Engineering Judgment (Stretch/Bonus)


  
**Next Steps:**



**Files modified:**


  
**Branch:**



**Repository:**


**Key commits:**


  
**Diff scope verification:**



**Approach decisions:**



---

## Pull Request

**PR Link:** 

**PR Description:**






Changes:


Testing:



**Maintainer Feedback:**



---

## Learnings & Reflections



### Technical Skills Gained



### Challenges Overcome



### What I'd Do Differently Next Time



---

## Resources Used


