<!-- Parent: sf-integration-procedure/SKILL.md -->
<!-- ABOUTME: Guide for using Integration Procedure REST API JSON templates. -->
<!-- ABOUTME: Documents required fields and the IsIntegrationProcedure discriminator. -->

# Integration Procedure Asset Templates

JSON body templates for creating Integration Procedures via REST API.

## Why REST API?

The `sf data create record --values` flag cannot handle JSON in textarea fields
like `PropertySetConfig`. Use `sf api request rest --method POST --body @file.json`
instead.

## Critical: IsIntegrationProcedure

OmniProcess stores both OmniScripts and Integration Procedures. The
`IsIntegrationProcedure` boolean is the **real discriminator**:

- `true` → Integration Procedure
- `false` (default) → OmniScript

The `OmniProcessType` picklist is **computed** from this boolean and cannot be set
directly via API. Always set `IsIntegrationProcedure: true` when creating IPs.

## Templates

| Template | Purpose |
|----------|---------|
| `omni-process-ip.json` | Create the parent OmniProcess record (IP shell) |
| `omni-process-element-dr-extract.json` | Add a DataRaptor Extract Action element |
| `omni-process-element-set-values.json` | Add a Set Values element |

## Usage

```bash
# 1. Create the IP (writes temp file, posts via REST)
cat omni-process-ip.json | sed 's/{{Type}}/MyType/g; s/{{SubType}}/MySubType/g' > /tmp/ip.json
sf api request rest /services/data/v66.0/sobjects/OmniProcess --method POST --body @/tmp/ip.json -o <org>

# 2. Add elements (use the returned Id as ParentProcessId)
cat omni-process-element-dr-extract.json | sed 's/{{ParentProcessId}}/0jNxxxx/g' > /tmp/elem.json
sf api request rest /services/data/v66.0/sobjects/OmniProcessElement --method POST --body @/tmp/elem.json -o <org>

# 3. Activate
sf api request rest /services/data/v66.0/sobjects/OmniProcess/<id> --method PATCH --body '{"IsActive": true}' -o <org>
```
