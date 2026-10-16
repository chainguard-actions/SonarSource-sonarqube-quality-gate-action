<!-- markdownlint-disable -->

# Hardening Report: SonarSource--sonarqube-quality-gate-action/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **SonarSource--sonarqube-quality-gate-action/v1.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block in action.yml directly interpolates `${{ inputs.scanMetadataReportFile }}` into the shell command string without routing through an `env:` variable. An attacker-controlled value (e.g. a file path containing shell metacharacters like `;`, `|`, `$(...)`) is substituted into the command before the shell parses it, enabling arbitrary command injection. Offending line: `run: $GITHUB_ACTION_PATH/script/check-quality-gate.sh ${{ inputs.scanMetadataReportFile }}`

Locations:

- `action.yml:11`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.scanMetadataReportFile  }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:12`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Moved `${{ inputs.scanMetadataReportFile }}` out of the `run:` block and into an `env:` block as `SCAN_METADATA_REPORT_FILE`. The shell command now references it as `"$SCAN_METADATA_REPORT_FILE"` (double-quoted), preventing shell metacharacters in the input from being interpreted as shell commands. Both findings were caused by the same inline expression interpolation on line 12 of action.yml.

