<!-- markdownlint-disable -->

# Hardening Report: SonarSource--sonarqube-quality-gate-action/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **SonarSource--sonarqube-quality-gate-action/v1.1.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): an expression is inserted directly into the composite step's run: line: `run: $GITHUB_ACTION_PATH/script/check-quality-gate.sh ${{ inputs.scanMetadataReportFile  }}`. The input is pasted into the shell script before bash runs it, and it is not quoted. A value containing shell metacharacters (;, |, $(...), backticks) would run as commands. To fix it, move the value into a step env: variable (e.g. SCAN_METADATA_REPORT_FILE: ${{ inputs.scanMetadataReportFile }}) and pass it double-quoted: "$GITHUB_ACTION_PATH/script/check-quality-gate.sh" "$SCAN_METADATA_REPORT_FILE".

Locations:

- `action.yml:11`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.scanMetadataReportFile  }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:12`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 9b3b9c7..99c7f92 100644
--- a/action.yml
+++ b/action.yml
@@ -7,8 +7,27 @@ branding:
 runs:
   using: "composite"
   steps:
     - id: quality-gate-check
-      run: $GITHUB_ACTION_PATH/script/check-quality-gate.sh ${{ inputs.scanMetadataReportFile  }}
+      env:
+        SCAN_METADATA_REPORT_FILE: ${{ inputs.scanMetadataReportFile }}
+      run: $GITHUB_ACTION_PATH/script/check-quality-gate.sh "$SCAN_METADATA_REPORT_FILE"
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

In action.yml, I moved the inputs.scanMetadataReportFile expression out of run: and into the step's env: block as SCAN_METADATA_REPORT_FILE. The script path and its argument are now both double-quoted. The script reads $1 as a single file path, so passing it as one quoted argument keeps upstream's behaviour. This re-applies the previous harden's fix. Not tested here.

