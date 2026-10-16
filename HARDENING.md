<!-- markdownlint-disable -->

# Hardening Report: SonarSource--sonarqube-quality-gate-action/v1.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **SonarSource--sonarqube-quality-gate-action/v1.2.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in a run: block. In action.yml line 12, both `${{ inputs.scanMetadataReportFile }}` and `${{ inputs.pollingTimeoutSec }}` are interpolated directly into the shell command string:

  run: $GITHUB_ACTION_PATH/script/check-quality-gate.sh "${{ inputs.scanMetadataReportFile  }}" "${{ inputs.pollingTimeoutSec }}"

The Actions runner substitutes these expressions into the shell command before the shell parses it, so an attacker-controlled input value containing shell metacharacters (e.g. `"; malicious-command #`) can inject arbitrary shell commands. The runtime validation inside check-quality-gate.sh (the `^[0-9]+$` regex on pollingTimeoutSec) does not prevent injection because it runs after the shell has already parsed and executed the injected content.

Fix: Move the inputs into env: variables and reference them as quoted shell variables:

  env:
    SCAN_METADATA_REPORT_FILE: ${{ inputs.scanMetadataReportFile }}
    POLLING_TIMEOUT_SEC: ${{ inputs.pollingTimeoutSec }}
  run: $GITHUB_ACTION_PATH/script/check-quality-gate.sh "$SCAN_METADATA_REPORT_FILE" "$POLLING_TIMEOUT_SEC"

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

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Fixed shell injection vulnerabilities in action.yml by moving both `${{ inputs.scanMetadataReportFile }}` and `${{ inputs.pollingTimeoutSec }}` from direct interpolation in the `run:` block into an `env:` block as `SCAN_METADATA_REPORT_FILE` and `POLLING_TIMEOUT_SEC` respectively. The shell script now references these as quoted environment variables (`"$SCAN_METADATA_REPORT_FILE"` and `"$POLLING_TIMEOUT_SEC"`), preventing attacker-controlled input values containing shell metacharacters from injecting arbitrary shell commands.

