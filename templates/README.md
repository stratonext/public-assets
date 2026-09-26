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
