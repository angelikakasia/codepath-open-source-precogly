# CodePath Open Source Capstone — Precogly

## Contribution 2: Fix Data Flow Asset Linking

**Student:** Angelika S.  
**Upstream repository:** [precogly/precogly](https://github.com/precogly/precogly)  
**Fork:** [angelikakasia/precogly](https://github.com/angelikakasia/precogly)  
**Issue:** [#203 — Data Flow: Cannot link Data Assets to a connection](https://github.com/precogly/precogly/issues/203)  
**Branch:** [`branch-203`](https://github.com/angelikakasia/precogly/tree/branch-203)  
**Commit:** [`d40e5e5`](https://github.com/angelikakasia/precogly/commit/d40e5e5918626963c509fd75e833a0afa8644c3b)  
**Pull request:** [#327 — fix: show data assets linked to threat-model flows](https://github.com/precogly/precogly/pull/327)  
**Current status:** PR open against upstream `main`; CLA signed; CI and maintainer review pending.

## Contribution Summary

Precogly's authenticated DFD Editor allowed a user to select a Data Asset and link it to a Data Flow, but the asset disappeared from the panel immediately afterward. I reproduced the issue, traced the request from React through Django REST Framework, found an incomplete organization filter in the backend, implemented a focused fix, added API regression tests, and verified manually that multiple linked assets and their protection settings survive a full page refresh.

---

# Phase I — Issue Selection and Community Engagement

## Why I Chose This Issue

Precogly is an OWASP threat-modeling project that supports structured approaches including STRIDE and PASTA. My security work has made me interested in threat modeling as part of application and AI security. Issue #203 was a useful contribution because it affected the accuracy of a threat model: users could not reliably document which Data Assets travel through a Data Flow.

The issue also required understanding the boundary between a React interface, a Django REST API, and tenant-aware database queries. That made it a good opportunity to improve my Django and open-source debugging skills while contributing a small, testable fix.

Vikram Narayan, Precogly's project lead, and I are both active in the security community. That connection helped me choose a project whose mission I understood, but the contribution still followed the project's normal public issue-assignment, testing, CLA, and review process.

<p align="center">
  <img
    src="https://github.com/user-attachments/assets/6017feca-0ae0-44ae-8344-7c69f0676f2c"
    alt="Angelika S. with Precogly project lead Vikram Narayan"
    width="1512"
  />
</p>

<p align="center"><em>With Precogly project lead Vikram Narayan.</em></p>

## Scope Confirmation

I introduced myself on Issue #203 and asked to work on it. Maintainer Vikram Narayan assigned the issue to me and clarified the scope:

- The authenticated DFD Editor is in scope.
- The Guest Editor is a separate code path and does not require changes.
- The frontend is React and the backend is Django.

Issue discussion: [precogly/precogly#203](https://github.com/precogly/precogly/issues/203)

## Initial Acceptance Criteria

- A user can link an existing Data Asset to a Data Flow.
- The linked asset appears in the Data Flow properties panel.
- The link remains visible after a page refresh.
- Existing Data Flow behavior continues to work.
- Automated tests cover the corrected path.

---

# Phase II — Environment, Reproduction, and Solution Plan

## Phase II Evidence at a Glance

| Rubric area | Evidence |
|---|---|
| Working branch | Fork and `branch-203` are linked at the top of this README. |
| Environment | Docker setup, commands, three real problems, and their resolutions are documented below. |
| Reproduction | Twelve numbered steps plus expected and actual behavior are included. |
| Code-level investigation | React hooks, API endpoint, serializer, viewset, model, and exact functions are named. |
| Solution plan | All six UMPIR sections are substantive and based on the confirmed root cause. |
| Engineering judgment | A false lead is ruled out and the tenant-isolation risk is addressed proactively. |

## Environment Setup

I used the project's Docker development environment so the React frontend, Django backend, and PostgreSQL database matched the repository's documented setup.

```bash
git clone https://github.com/angelikakasia/precogly.git
cd precogly
git checkout branch-203
docker compose up --build
```

Local environment:

- macOS
- Docker Desktop and Docker Compose
- React frontend: `http://localhost:5173`
- Django backend: `http://localhost:8000`
- PostgreSQL 16 container
- Issue branch: `branch-203`

## Setup Challenges and Resolutions

### Docker build and pip warning

The first image build took several minutes, and pip warned that it was running as root inside the backend container. I allowed the build to finish because the warning did not prevent the development image from starting.

### Port 8000 already in use

A stale backend process/container was holding port 8000. I stopped the stale process before restarting the Compose stack.

### PostgreSQL hostname resolution

The backend later reported:

```text
failed to resolve host 'db': Temporary failure in name resolution
```

The Compose file already defined the PostgreSQL service as `db`, so changing YAML would not have addressed the cause. I removed the stale Precogly containers/network in Docker Desktop and recreated the stack with `docker compose up --build`. These were local environment problems, not Issue #203 code changes.

## Reproduction Steps

1. Start the local stack with `docker compose up --build` and open `http://localhost:5173`.
2. Sign in with the seeded development account.
3. Open an existing Threat Model or create a new one.
4. Open the authenticated DFD Editor, not the Guest Editor.
5. Add two components, such as a Human Actor and Process.
6. Draw a Data Flow between the components.
7. Define at least one Data Asset in System Context.
8. Select the Data Flow to open its properties panel.
9. Scroll to **Data Assets** and click **Link Asset**.
10. Select an available asset and click **Link**.
11. Observe that the selector closes but the asset is missing from the linked-assets list.
12. Refresh the page, reopen the same flow, and observe that the asset remains absent.

## Expected and Actual Behavior

**Expected:** Clicking **Link** should attach the selected Data Asset to the Data Flow, display it in the properties panel, and continue displaying it after a refresh.

**Actual before the fix:** The create request completed and the selector closed, but the follow-up list request did not return the new association. The UI therefore looked as though the asset had not been saved.

Before-fix evidence: [Issue #203 description and reproduction image](https://github.com/precogly/precogly/issues/203)

## Code Investigation

I traced the complete authenticated-editor path:

1. `frontend/src/features/dfd-editor/components/panels/EdgeEditPanel.tsx`
   - `DataFlowDataAssetsSection` renders linked and available assets.
   - `handleLinkAsset()` sends the selected flow and asset IDs.
2. `frontend/src/features/threat-models/api/data-flow-assets.ts`
   - `useCreateDataFlowAsset()` sends `POST /api/data-flow-assets/`.
   - `useDataFlowAssets()` reloads links with `GET /api/data-flow-assets/?data_flow=<id>`.
3. `backend/apps/systems/urls.py`
   - Routes `/api/data-flow-assets/` to `DataFlowAssetViewSet`.
4. `backend/apps/systems/serializers.py`
   - `DataFlowAssetSerializer` accepts and returns the association fields.
5. `backend/apps/systems/views.py`
   - `DataFlowAssetViewSet.get_queryset()` decides which links the user can retrieve.
6. `backend/apps/systems/models.py`
   - `DataFlowAsset` stores the unique Data Flow–Data Asset association.

### False lead ruled out

The React request uses `dataFlow`, `dataAsset`, and `protectionMethod`, while Django fields use `data_flow`, `data_asset`, and `protection_method`. I verified that this is intentional: Precogly uses `djangorestframework-camel-case` to convert JSON keys at the API boundary. Manually changing the frontend fields to snake_case would have violated the project's API convention and would not have fixed the problem.

## Confirmed Root Cause

The POST serializer created the `DataFlowAsset` record, but `DataFlowAssetViewSet.get_queryset()` only returned associations reachable through this ownership path:

```text
Data Flow → source component → orgsystem → organization
```

The authenticated DFD Editor also creates valid components with `orgsystem = null` that belong directly to a Threat Model:

```text
Data Flow → source component → threat model → organization
```

After a successful POST, React Query invalidated and reloaded the linked-assets query. Because the viewset did not recognize the direct Threat Model path, the GET response hid the newly created record. This exactly matched the observed behavior: the selector closed on success, but no linked asset appeared.

## UMPIR Solution Plan

### Understand

Trace the behavior from `handleLinkAsset()` through the React Query create and list hooks, DRF routing, serializer validation, model relationship, and tenant-filtered queryset. Separate record creation from record visibility instead of assuming the POST failed.

### Match

Use `DataFlowInstanceThreatViewSet.get_queryset()` in `backend/apps/threats/views.py` as the project-specific match. That endpoint already authorizes Data Flow records through either the source component's orgsystem or its direct Threat Model when the orgsystem is null. Reusing this pattern keeps access behavior consistent across Data Flow features.

### Plan

Change only `DataFlowAssetViewSet.get_queryset()` to support both ownership paths. Preserve the original orgsystem path, add the direct Threat Model path with Django `Q` expressions, and order results for stable pagination. Add a positive API regression test for the bug and a negative cross-organization test because broadening an authorization query can create a data-isolation risk.

### Implement

Update the backend queryset and add focused tests. Do not change the Guest Editor, React request format, serializer, model, or database schema because those pieces already work.

### Review

Inspect the final diff for unrelated changes, verify Python syntax and whitespace, run the focused API tests, and manually exercise the link and refresh paths. Confirm that already-linked assets are removed from the selector and protection settings persist.

### Evaluate

Verify the happy path, multiple links, page-refresh persistence, protection-setting updates, and cross-organization isolation. Open a non-draft PR against upstream `main` and request human maintainer review.

## Phase II Engineering Judgment

The important debugging decision was to distinguish persistence from visibility. The UI's success behavior suggested that the POST might have worked, so I followed the subsequent GET instead of rewriting the React handler. I also treated the queryset change as security-sensitive and planned a negative tenant-isolation test, not only a happy-path test.

---

# Phase III — Implementation and Testing

## Phase III Evidence at a Glance

| Rubric area | Evidence |
|---|---|
| Implementation | Commit `d40e5e5`, exact file paths, focused diff, and queryset change are documented. |
| New tests | Two API tests directly exercise the fixed path and cross-organization isolation. |
| Existing suite | Full backend suite passed: `154 passed, 8 pre-existing warnings in 6.17s`. |
| Project patterns | Tests use DRF `APITestCase`, authenticated clients, Django models, and existing naming conventions. |
| Challenges | POST/GET mismatch, API casing, and pagination ordering are explained with resolutions. |
| Testing notes | Focused, full-suite, static, and manual tests are documented separately. |

## Implementation

Commit: [`d40e5e5 — fix: show data assets linked to threat-model flows`](https://github.com/angelikakasia/precogly/commit/d40e5e5918626963c509fd75e833a0afa8644c3b)

### Queryset fix

Updated `DataFlowAssetViewSet.get_queryset()` in `backend/apps/systems/views.py` to return associations authorized through either supported ownership path:

```python
return DataFlowAsset.objects.filter(
    Q(data_flow__source_component__orgsystem__organization_id__in=org_ids)
    | Q(
        data_flow__source_component__orgsystem__isnull=True,
        data_flow__source_component__threat_model__organization_id__in=org_ids,
    )
).select_related(
    "data_flow__source_component", "data_flow__dest_component", "data_asset"
).order_by("id")
```

The direct `.order_by("id")` keeps paginated API results stable and removes `UnorderedObjectListWarning` from the focused tests.

### Files changed

- `backend/apps/systems/views.py` — supports both organization ownership paths and stable ordering.
- `backend/apps/systems/tests/__init__.py` — makes the systems test directory a Python test package.
- `backend/apps/systems/tests/test_data_flow_assets.py` — adds two API regression tests.

The commit contains 97 additions and 3 deletions. No frontend files, migrations, generated files, credentials, or unrelated formatting were included.

## Automated Tests Added

### `test_created_asset_link_is_returned_for_threat_model_components`

This test recreates the failing path. It creates a member, organization, Threat Model, two directly owned components, a Data Flow, and a Data Asset. It sends the same camelCase POST payload as React, lists the associations again through the filtered endpoint, and verifies that the created asset link is returned.

### `test_asset_links_from_other_organizations_are_not_returned`

This test covers the authorization edge case introduced by broadening the query. It creates a Data Flow Asset association owned by a different organization and verifies that the authenticated user receives zero results.

Focused test command:

```bash
docker compose exec -T backend \
  pytest apps/systems/tests/test_data_flow_assets.py -q
```

Result:

```text
2 passed, 8 warnings in 3.45s
```

The eight warnings are pre-existing `RemovedInDjango60Warning` messages from historical threat migration files that use the deprecated `CheckConstraint.check` argument. The warning caused by the new endpoint's unordered pagination was removed by ordering the queryset directly.

## Full Backend Test Suite

I also ran the complete backend test suite to check for regressions outside the focused endpoint:

```bash
docker compose exec -T backend pytest -q
```

Result:

```text
154 passed, 8 warnings in 6.17s
```

All existing backend tests passed. The eight warnings are the same pre-existing Django 6.0 deprecation warnings from historical migration files described above; no test failed because of this change.

## Static Checks

The following checks passed:

```bash
git diff --check
python3 -m compileall -q \
  backend/apps/systems/views.py \
  backend/apps/systems/tests/test_data_flow_assets.py
```

## Manual Testing

I tested the fix in the authenticated DFD Editor using the `PASTA` Threat Model and `Data Flow Diagram 1`:

1. Selected the flow from `New Human Actor` to `New Process`.
2. Confirmed the panel initially displayed linked assets.
3. Linked `Employee Records` and confirmed the count increased to three.
4. Set protection methods to `Masked`, `Hashed`, and `Masked` for the three assets.
5. Performed a full browser refresh and reopened the same Data Flow.
6. Confirmed all three assets and all three protection settings remained visible.
7. Opened **Link Asset** and confirmed already-linked assets were excluded from the available choices.

Manual result: linking, display, multiple associations, protection updates, duplicate prevention, and refresh persistence all passed.

After-fix evidence: add the final after-refresh screenshot here after uploading it to this README or link to the image in [PR #327](https://github.com/precogly/precogly/pull/327).

## Challenges Faced During Implementation

### The POST looked successful but the feature looked broken

The main challenge was that the React success callback ran even though the asset disappeared. Tracing both requests showed that creation and retrieval had different behavior: the POST created the database record, but the tenant-filtered GET hid it. Separating those two operations led to the actual cause.

### Request field names looked inconsistent

React used camelCase while Django used snake_case. Reading the repository's API settings and coding standards showed that automatic conversion was intentional, so I avoided an incorrect frontend workaround.

### Pagination warning during tests

The first focused test run passed but reported `UnorderedObjectListWarning`. A viewset `ordering` attribute alone did not affect this endpoint because it did not use DRF's ordering filter backend. Applying `.order_by("id")` directly to the queryset removed that warning. The remaining warnings came only from unrelated historical migrations.

## Phase III Engineering Judgment

The smallest correct fix was one authorization-query change rather than a frontend rewrite. The new test uses the real API boundary rather than testing only a helper, and the second test protects tenant isolation. This matches the project's existing DRF testing style and addresses both functionality and security.

---

# Phase IV — Pull Request, Review, and Reflection

## Phase IV Evidence at a Glance

| Rubric area | Evidence |
|---|---|
| Upstream PR | PR #327 is open, not draft, from the fork to upstream `main`. |
| Issue reference | The description uses `Closes #203`. |
| PR quality | Root cause appears before implementation details, followed by tests and acceptance criteria. |
| Evidence | The issue supplies before-fix evidence; the final after-refresh screenshot must be embedded below. |
| README status | PR link, summary, CLA state, test state, and review state are recorded. |
| Reflection | Technical skills, judgment, challenges, improvements, and a broader takeaway are included. |

## Pull Request

- **PR:** [precogly/precogly#327](https://github.com/precogly/precogly/pull/327)
- **Title:** `fix: show data assets linked to threat-model flows`
- **Base:** `precogly/precogly:main`
- **Head:** `angelikakasia/precogly:branch-203`
- **Type:** Open, not draft
- **Linked issue:** `Closes #203`
- **CLA:** Signed
- **Current review status:** Maintainer workflow approval and approving review pending

## Why Before What

Users could select a Data Asset for a Data Flow, but the linked record disappeared from the authenticated editor because the API's list query did not recognize components owned directly by a Threat Model. This made the model look incomplete even though the association had been created.

The fix updates the backend ownership filter to recognize both valid component ownership paths and adds regression tests for the fixed behavior and cross-organization isolation. No frontend or Guest Editor changes were necessary.

## Acceptance Criteria

- [x] An existing Data Asset can be linked to a Data Flow.
- [x] The linked asset appears in the Data Flow properties panel.
- [x] Multiple assets can be linked to the same flow.
- [x] Linked assets remain visible after a full page refresh.
- [x] Protection settings remain visible after a refresh.
- [x] Already-linked assets are excluded from the selection list.
- [x] Cross-organization associations remain hidden.
- [x] Automated API regression tests pass.
- [x] The authenticated DFD Editor/backend path is fixed without changing the Guest Editor.

## Before and After Evidence

**Before:** Issue #203 contains the original reproduction and image showing that the asset does not remain linked: [before-fix report](https://github.com/precogly/precogly/issues/203).

**After:** Upload the final screenshot showing three assets after refresh here and to PR #327. Suggested caption: `After refresh: Customer Data, API Keys, and Employee Records remain linked with their protection settings.`

## Maintainer Feedback Log

| Date | Feedback | Response | Commit |
|---|---|---|---|
| Pending | PR is awaiting initial maintainer review. | I will record each requested change and my response here. | Pending |

## Learnings and Reflections

### Technical skills gained

I learned to trace one user action across React state, React Query mutations and invalidation, DRF serializers and viewsets, Django ORM relationships, and PostgreSQL persistence. I also learned that a successful write does not guarantee that the following read uses the same authorization path.

### Engineering judgment

I initially considered changing the frontend request fields, but repository configuration proved that camelCase conversion was intentional. The better fix was to reuse an existing tenant-query pattern from the Data Flow threat endpoint. I also learned that expanding a queryset requires a negative isolation test, because a functional fix must not expose another organization's data.

### Challenges overcome

I recovered from Docker networking and port conflicts, reproduced a bug across two application layers, separated record creation from record visibility, corrected a pagination warning, and completed both automated and manual verification.

### What I would do differently

Next time I would capture the browser's POST and GET responses during the first reproduction. Seeing a successful POST followed by an empty GET would have narrowed the problem to retrieval or authorization earlier. I would also make smaller commits as the investigation, fix, and tests progress so reviewers can follow the development history more easily.

### Broader takeaway

In multi-tenant applications, authorization filters are part of feature correctness. A record can exist in the database and still appear lost if create and list endpoints do not agree on ownership. When a UI briefly succeeds and then loses data, inspect the refetch and tenant scope before rewriting the UI.

## Final Status

The fix is implemented, pushed, tested, and submitted upstream in PR #327. The CLA is signed. The remaining repository steps—workflow approval, CI completion, maintainer review, and merge—require upstream maintainer action.

---

# CodePath Submission Checklist


- [x] Run the full backend suite: `154 passed, 8 pre-existing warnings in 6.17s`.
- [x] Submit the Phase II check-in and mark Phase II complete.
- [x] Submit the Phase III check-in and mark Phase III complete.
- [x] Submit the Phase IV check-in and mark Phase IV complete.
- [x] Paste this GitHub README URL into the CodePath portal.
- [x] Add any required Slack participation links or screenshots.
- [x] Ask the maintainer to approve the first-contributor workflow and review PR #327.
- [ ] Record maintainer feedback and response commits in the feedback log when received.
- [ ] Update the final PR status after CI, review, or merge.

## Resources Used

- [Precogly repository](https://github.com/precogly/precogly)
- [Issue #203](https://github.com/precogly/precogly/issues/203)
- [Pull request #327](https://github.com/precogly/precogly/pull/327)
- [Precogly contributing guide](https://github.com/precogly/precogly/blob/main/CONTRIBUTING.md)
- [Precogly coding standards](https://github.com/precogly/precogly/blob/main/CODING_STANDARDS.md)
- Django REST Framework API tests and queryset patterns already present in the repository
