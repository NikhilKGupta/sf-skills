<!-- Parent: sf-flexcard/SKILL.md -->
<!-- ABOUTME: Guide for using FlexCard REST API JSON templates. -->
<!-- ABOUTME: Documents OmniUiCard fields and DataSourceConfig/PropertySetConfig separation. -->

# FlexCard Asset Templates

JSON body templates for creating FlexCards via REST API.

## Why REST API?

The `sf data create record --values` flag cannot handle JSON in textarea fields
like `DataSourceConfig` and `PropertySetConfig`. Use
`sf api request rest --method POST --body @file.json` instead.

## Critical: OmniUiCard Fields

FlexCards use `OmniUiCard`, NOT `OmniProcess`. Key fields:

| Field | Purpose |
|-------|---------|
| `Name` | Card name (required) |
| `DataSourceConfig` | JSON — data source bindings (IP references, input maps) |
| `PropertySetConfig` | JSON — card layout, states, field definitions |
| `OmniUiCardType` | `Child` (standard) or `Parent` |
| `VersionNumber` | Integer, starts at 1 |
| `IsActive` | Boolean, set to true after creation |

There is **no `Definition` field** on `OmniUiCard` in Core namespace.

## Data Source Type

When binding to an Integration Procedure, the type value is
`IntegrationProcedures` (plural, capital P). Not `IntegrationProcedure`.

## Templates

| Template | Purpose |
|----------|---------|
| `omni-ui-card.json` | Create a FlexCard with IP data source binding |

## Usage

```bash
# 1. Create the FlexCard
sf api request rest /services/data/v66.0/sobjects/OmniUiCard --method POST --body @omni-ui-card.json -o <org>

# 2. Activate
sf api request rest /services/data/v66.0/sobjects/OmniUiCard/<id> --method PATCH --body '{"IsActive": true}' -o <org>
```

## OmniFlexCardView

The FlexCard Designer uses `OmniFlexCardView` as a virtual object for its list
view (`/lightning/o/OmniFlexCardView/home`). This is separate from the `OmniUiCard`
sObject where card records are stored. Cards created via API may not appear in
the "Recently Viewed" list in the Designer until opened there.
