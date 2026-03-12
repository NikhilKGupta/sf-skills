<!-- Parent: sf-omniscript/SKILL.md -->
<!-- ABOUTME: Guide for using OmniScript REST API JSON templates. -->
<!-- ABOUTME: Documents required fields and the IsIntegrationProcedure=false default. -->

# OmniScript Asset Templates

JSON body templates for creating OmniScripts via REST API.

## Why REST API?

The `sf data create record --values` flag cannot handle JSON in textarea fields
like `PropertySetConfig`. Use `sf api request rest --method POST --body @file.json`
instead.

## OmniScript vs Integration Procedure

Both use the `OmniProcess` sObject. OmniScripts have `IsIntegrationProcedure = false`
(the default). The `OmniProcessType` picklist is computed — do not set it directly.

## Templates

| Template | Purpose |
|----------|---------|
| `omni-process-omniscript.json` | Create the parent OmniProcess record (OmniScript shell) |
| `omni-process-element-step.json` | Add a Step element (top-level container) |
| `omni-process-element-text-block.json` | Add a Text Block inside a Step |

## Usage

```bash
# 1. Create the OmniScript shell
sf api request rest /services/data/v66.0/sobjects/OmniProcess --method POST --body @omni-process-omniscript.json -o <org>

# 2. Add Step elements (Level 0)
sf api request rest /services/data/v66.0/sobjects/OmniProcessElement --method POST --body @omni-process-element-step.json -o <org>

# 3. Add child elements inside steps (Level 1, ParentElementId = Step Id)
sf api request rest /services/data/v66.0/sobjects/OmniProcessElement --method POST --body @omni-process-element-text-block.json -o <org>

# 4. Activate
sf api request rest /services/data/v66.0/sobjects/OmniProcess/<id> --method PATCH --body '{"IsActive": true}' -o <org>
```
