# StratoNext template gallery

Templates that show up in StratoNext's in-app template gallery, pre-made
starting points a customer can browse and import into their own workspace
with one click.

## File format

A template file has two parts, separated by `---`:

1. **Frontmatter**: metadata (`id`, `name`, `description`, `cloudProvider`,
   `category`, `version`, `tags`).
2. **Body**: `task` (what gets created on import) and `placeholders` (what
   the user fills in before importing).

```yaml
---
id: aws-cost-and-credits-coverage
name: AWS cost and credits coverage report
description: Aggregates AWS spend against applied credits...
cloudProvider: AWS
category: cost-optimization
version: 1
tags: [billing, credits]
---
task:
  name: AWS cost and credits coverage report
  description: ...
  instructions: |
    Using AWS Cost Explorer...
placeholders:
  environment: ...
  agent: ...
  variables: [...]
```


## Variable types

A `placeholders.variables[]` entry's `type` is usually `string`, `number`, or
`boolean` — rendered as a plain text input, number input, or toggle.

A few types are **native**: the app renders a real picker or a validated
input instead of a plain text box.

- `aws-region` — a Select of known AWS regions. Don't give an enum or example
  text in the `description`; the picker already constrains the value.
- `aws-account-id` — a text input constrained to exactly 12 digits, matching
  how the app validates an environment's own AWS account ID field.

More native types will follow the same `<provider>-<resource>` naming
convention (e.g. a future `aws-account` picker of the user's own linked
accounts). If a variable doesn't fit an existing native type, `type: string`
with a clear `description` is still the right default.

## Adding a template

1. Copy `aws/aws-cost-and-credits-coverage.yaml` (in this folder) as your
   starting point and fill it in. `stratonext-template-schema.yaml` is the
   full JSON Schema it is validated against, if you need the exact rules.
2. Save it under `templates/<cloudProvider>/<id>.yaml`, where `<cloudProvider>`
   is a lowercase folder name (`aws`, `azure`, `gcp`, ...) and `<id>` matches
   the `id:` field inside the file.
3. Add one entry for it to `index.json` in this folder:

   ```json
   {
     "id": "<id>",
     "name": "<name>",
     "description": "<description>",
     "cloudProvider": "<cloudProvider>",
     "category": "<category>",
     "path": "templates/<cloudProvider>/<id>.yaml"
   }
   ```
