<!-- Parent: sf-datamapper/SKILL.md -->
<!-- ABOUTME: Guide for using Data Mapper REST API JSON templates. -->
<!-- ABOUTME: Documents OmniDataTransform/OmniDataTransformItem creation patterns. -->

# Data Mapper Asset Templates

JSON body templates for creating Data Mappers via REST API.

## Why REST API?

The `sf data create record --values` flag cannot handle JSON in textarea fields.
Use `sf api request rest --method POST --body @file.json` instead.

## Critical: Foreign Key Field Name

The parent lookup on `OmniDataTransformItem` is `OmniDataTransformationId`
(full word "Transformation"), NOT `OmniDataTransformId`. This is a common
source of SOQL and API errors.

## Critical: Draft vs Active

Draft Data Mappers cannot be retrieved via metadata API. The command
`sf project retrieve start -m OmniDataTransform:<Name>` only works for
**active** Data Mappers. Draft records return "Entity cannot be found".

## Templates

| Template | Purpose |
|----------|---------|
| `omni-data-transform-extract.json` | Create an Extract Data Mapper |
| `omni-data-transform-load.json` | Create a Load Data Mapper |
| `omni-data-transform-transform.json` | Create a Transform Data Mapper |
| `omni-data-transform-item.json` | Add a field mapping item to any Data Mapper |

## Usage

```bash
# 1. Create the Data Mapper
sf api request rest /services/data/v66.0/sobjects/OmniDataTransform --method POST --body @omni-data-transform-extract.json -o <org>

# 2. Add field mapping items (use returned Id as OmniDataTransformationId)
sf api request rest /services/data/v66.0/sobjects/OmniDataTransformItem --method POST --body @omni-data-transform-item.json -o <org>

# 3. Activate
sf api request rest /services/data/v66.0/sobjects/OmniDataTransform/<id> --method PATCH --body '{"IsActive__c": true}' -o <org>
```

## Naming Convention

Follow the pattern: `DR_[Type]_[Object]_[Purpose]`

| Type | Prefix Example |
|------|---------------|
| Extract | `DR_Extract_Account_Details` |
| Turbo Extract | `DR_TurboExtract_Case_List` |
| Transform | `DR_Transform_Lead_Flatten` |
| Load | `DR_Load_Opportunity_Create` |
