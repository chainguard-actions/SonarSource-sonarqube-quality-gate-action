<!-- markdownlint-disable -->

# Hardening Report: SonarSource--sonarqube-quality-gate-action/v1.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **SonarSource--sonarqube-quality-gate-action/v1.2.1** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) is violated: GitHub Actions expressions are placed directly inside the composite step's run: command. Offending line: `run: $GITHUB_ACTION_PATH/script/check-quality-gate.sh "${{ inputs.scanMetadataReportFile  }}" "${{ inputs.pollingTimeoutSec }}"`. The values are substituted into the script text before bash parses it, so an input containing a double quote, $(...) or backticks can run commands. Fix: pass the inputs through env: variables (for example INPUT_SCAN_METADATA_REPORT_FILE and INPUT_POLLING_TIMEOUT_SEC) and refer to them double-quoted in the script.

Locations:

- `action.yml:12`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.scanMetadataReportFile  }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:13`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.pollingTimeoutSec }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:13`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 85de7c6..0ba7972 100644
--- a/action.yml
+++ b/action.yml
@@ -8,8 +8,28 @@ branding:
 runs:
   using: "composite"
   steps:
     - id: quality-gate-check
-      run: $GITHUB_ACTION_PATH/script/check-quality-gate.sh "${{ inputs.scanMetadataReportFile  }}" "${{ inputs.pollingTimeoutSec }}"
+      env:
+        SCAN_METADATA_REPORT_FILE: ${{ inputs.scanMetadataReportFile }}
+        POLLING_TIMEOUT_SEC: ${{ inputs.pollingTimeoutSec }}
+      run: $GITHUB_ACTION_PATH/script/check-quality-gate.sh "$SCAN_METADATA_REPORT_FILE" "$POLLING_TIMEOUT_SEC"
       shell: bash
 inputs:
   scanMetadataReportFile:

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, prior-fix

**Notes:**

Moved inputs.scanMetadataReportFile and inputs.pollingTimeoutSec into the step's env: block (SCAN_METADATA_REPORT_FILE, POLLING_TIMEOUT_SEC) and referenced them double-quoted in run:; also quoted the script path.

