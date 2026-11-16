# Workflows

`stratonext-workflow-schema.yaml` is the JSON Schema (draft 2020-12, written in YAML) of a StratoNext
workflow file: the YAML behind every workflow in the editor. Its raw URL is its `$id`:

    https://raw.githubusercontent.com/stratonext/public-assets/main/workflows/stratonext-workflow-schema.yaml

Use it to validate a workflow before publishing it, from an editor (the YAML language server's
`yaml.schemas` setting, or a `# yaml-language-server: $schema=<url>` first line in the file) or from CI.
The editor's **Schema** link opens it. The language itself is documented at
https://docs.stratonext.ai/workflows/ and the node types at https://docs.stratonext.ai/workflows/nodes/.

## Regenerating it

The platform builds the schema from the node types it ships, so this file follows the platform. It is
served, without sign-in, at `GET https://api.prod.stratonext.com/v1/workflows/schema`; regenerate after
a release that adds or changes a node type:

    curl -s https://api.prod.stratonext.com/v1/workflows/schema \
      | python3 -c 'import json,sys,yaml; print(yaml.safe_dump(json.load(sys.stdin), sort_keys=False, width=100))' \
      > body.yaml

then replace everything below the comment header of `stratonext-workflow-schema.yaml` with `body.yaml`.
