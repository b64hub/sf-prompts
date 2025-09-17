---
description: Git Instructions
---
# Git instructions 

Short instruction for working with git

## Branch naming

Branches should be named using the pattern:

  {jira-ticket}-short-description

Rules:

- Include the Jira ticket key at the start (e.g. `bt-3210`).
- Follow immediately with a hyphen and a short, kebab-case description.
- Use only lowercase letters, numbers and hyphens.
- Keep the descriptive part succinct (prefer <= 50 characters).
- Examples: `bt-3210-add-login`, `bt-3122-fix-null-check`, `bt-100-docs-update-readme`.

Assumption: Jira task keys are available and teams will include them; if no ticket exists, use `chore-` as a prefix followed by the short description.

## Commit message format

This section is only relevant for creating Git commit messages.

When I ask you to generate a Git commit message, follow these rules:

### 1) Conventional Commit Types

Always prefix with one of:

- **feat:** new feature or functionality
- **fix:** bug fix
- **chore:** non-production code changes (config, CI, dependencies)
- **docs:** documentation changes
- **refactor:** code refactoring without changing behavior
- **style:** formatting/style-only changes
- **test:** add or refactor tests (use `feat` if adding a new feature with tests)

### 2) Subject Line Rules

- Format: <type>: <jira-task> <short subject>
- `<jira-task>` must be included and taken from the **current branch name**  
  (e.g. if branch is `feature/bt-3210-dashboard-filters`, then use `bt-3210`).
- Use **lowercase** for the Jira task key (e.g. `bt-1234`).
- Use **imperative mood** ("add", "fix", not "added", "fixes").
- Keep subject **short & sweet** (≤ 50 characters, excluding Jira task).
- Do not capitalize the first word after the Jira task.
- Do not end with a period (`.`).

### 3) Body Rules

- Keep the body optional—prefer short, clear subjects.
- If needed, separate subject from body with a blank line.
- Wrap body lines at 72 characters.
- Explain **what** and **why**, not **how**.
- Reference additional Jira issues or context if relevant.

### 4) Examples

- feat: bt-3210 add filtering by last modified date
- fix: bt-3122 add null check to avoid throwing error
- docs: bt-3150 add package diagram to readme
- refactor: bt-3190 tidy up RambaseAPI according to new framework

---

## 🔍 Pull Request Review Agent

### 📋 Instructions

- Run file listing commands locally (don’t ask user):
  - Modified files: `git fetch origin && git diff --name-only origin/main...HEAD`
  - Uncommitted files: `git status --porcelain`
- Always include the list of reviewed files in feedback.
- Feedback must be a single structured message covering **all sections in order**.  
  If no findings, state “No issues” or “Not Applicable (why)”.
- Run tests if explicitly asked to. Run only tests related to modified code and report on coverage.

### 📊 Feedback Format

1. **📁 Modified Files** – list all files
2. **❌ Issues Found** – critical blockers
3. **✅ Passed Checks** – standards met
4. **⚠️ Reminders** – notes for developer
5. **🎯 Required Actions** – specific next steps

---

### 1) General Sanity

- Confirm commands executed and file list correct.

---

### 2) File Type Checks

#### `.cls` Files

- Remove all `System.debug`.
- API version = latest.
- No files in `src-tmp/**` in the PR.
- No hardcoded strings (use Constants class).
- Test coverage ≥ 80%. If tests are included in the PR, ask if you should run them if not explicitly told so already.

##### Factory Pattern

- Queries use `factory.repoFactory.getRepo*().execute()`.
- DML via `factory.repoFactory.getDML()`.
- No direct SOQL/DML.
- Services instantiated with factory injection.

##### Test Classes

- Name methods: 
  - `should_action_scenario_withMocks`.
  - `should_action_scenario_withFakes`.
  - `should_action_scenario`.
- Annotated `@IsTest`.
- Init with `PackageFactory.initWithMocks()`.
- Structure: `// Arrange`, `// Act`, `// Assert`.
- Use `Assert` methods, not `System.assert`.
- Services created via factory.

##### Controllers

- Verify CRUD/FLS and class access in relevant permission sets.
  - Remind user to update permission sets if not done.
- New controllers must be granted access.

#### `.trigger` Files

- At least one real test executes DML + asserts.
- Follow Trigger Action naming: `TA_Object_TIMING_ActionDescription`:
  - `TA_Lead_BI_UpdateIndustry` (Before Insert)
  - `TA_Lead_BU_UpdateIndustry` (Before Update)
  - `TA_Lead_AI_UpdateIndustry` (After Insert)
  - `TA_Lead_AU_UpdateIndustry` (After Update)
  - `TA_Lead_BIAU_UpdateIndustry` (Before Insert + After Update)
  - `TA_Lead_AIAU_UpdateIndustry` (After Insert + After Update)

#### `.js` Files

- Remove `console.log`.
- Remove unused imports, vars, methods.

#### `.standardValueSet-meta.xml`

- Remind user of manual deployment.

---

### 3) Deletions

- Fields: mark with `<businessStatus>DeprecateCandidate</businessStatus>` + manifest entry.
- Apex classes: replace body with `// TODO: delete me` + manifest entry. Add @IsTest at top of the class.
- Trigger Action Custom Metadata (CMDT): `Bypass_Execution__c = true`, `Description__c = 'TODO: delete me'`. Cover all timing variants. Examples:
  - `TA_Lead_BI_UpdateIndustry` (Before Insert)
  - `TA_Lead_BU_UpdateIndustry` (Before Update)
  - `TA_Lead_AI_UpdateIndustry` (After Insert)
  - `TA_Lead_AU_UpdateIndustry` (After Update)
  - `TA_Lead_BIAU_UpdateIndustry` (Before Insert + After Update)
  - `TA_Lead_AIAU_UpdateIndustry` (After Insert + After Update)
- Manifest: pre vs post choice documented, all staged items listed once, Jira ID in PR, API names exact.
- Reference cleanup: no orphan refs, permission sets updated, tests adjusted, validation-only deploy succeeds.

---

### 4) Final Confirmation

- All checks complete.
- Auto-fixes applied.
- Coverage ≥ 80% confirmed.
- Destructive changes validated.
- Ready for reviewer assignment.

---
