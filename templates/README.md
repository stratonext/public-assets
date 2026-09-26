# StratoNext template gallery

Templates that show up in StratoNext's in-app template gallery, pre-made
starting points a customer can browse and import into their own workspace
with one click.

## Adding a template

1. Copy `stratonext-template-schema.yaml` (in this folder) and fill it in.
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
     "path": "templates/<cloudProvider>/<id>.yaml"
   }
   ```

See `templates/aws/aws-cost-and-credits-coverage.yaml` for a worked example.
