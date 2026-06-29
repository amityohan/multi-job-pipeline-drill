# Pipeline Audit

## 1. Lint

**Purpose:**
Checks the code for style and syntax errors.

**Problem:**
Runs in parallel with every other job. If lint fails, the remaining jobs still execute.

**Fix:**
Make lint the first job by having other jobs depend on it using `needs:`.

---

## 2. Unit Tests

**Purpose:**
Runs the application's unit tests.

**Problem:**
Starts immediately without waiting for lint to finish.

**Fix:**
Add `needs: lint`.

---

## 3. Build

**Purpose:**
Builds the application and creates the `dist/` folder.

**Problem:**
Does not upload the build artifact, so other jobs cannot access it.

**Fix:**
Upload the `dist/` folder using `actions/upload-artifact`.

---

## 4. Integration Tests

**Purpose:**
Runs integration tests using the built application.

**Problem:**
Does not download the build artifact and therefore cannot access the `dist/` directory.

**Fix:**
Download the artifact using `actions/download-artifact` and make the job depend on the build job.

---

## 5. Deploy Staging

**Purpose:**
Deploys the application to the staging environment.

**Problem:**
Runs on every branch and does not wait for all required tests.

**Fix:**
Run only on the `main` branch after both unit tests and integration tests pass.

---

## 6. Deploy Production

**Purpose:**
Deploys the application to production.

**Problem:**
Runs on every push, including feature branches.

**Fix:**
Deploy only after the staging deployment succeeds and only from the `main` branch.

---

## 7. Notify

**Purpose:**
Sends a notification after the pipeline completes.

**Problem:**
Does not use `if: always()`, so it may be skipped if an earlier job fails.

**Fix:**
Add `if: always()` so it always executes.