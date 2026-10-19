# Drift report: one-time prompt

Paste the prompt below into an agent chat that has the StratoNext skill
installed. Fill in the values in the **Inputs** block first.

The agent downloads the project and deployment definitions, compares what
the deployment defines with what is actually running in the cloud, and
reports every resource that is missing, unmanaged, or configured
differently. It runs once, right in the chat, and produces the report as
Markdown. It creates no template or task.

---

```text
Check one deployment of one of my projects for configuration drift: where
what is running in the cloud no longer matches what the deployment
defines. Do this now, in this chat, and give me the report. Do not create
a template or task.

## Inputs

- Project: <project name or id>
- Deployment: <deployment name or id>
- Compare tags: yes
- Look for unmanaged resources: yes

## Step 1. Download the project and deployment

Use the StratoNext skill to download:
- The project: its name, description, owners or teams, and its list of
  deployments.
- The deployment: its environment, cloud provider, accounts or
  subscriptions, regions, and the full definition of every resource it
  declares (type, identifier or name, size or instance type, version,
  scaling settings, network and security group rules, encryption, public
  access settings, tags), and the tags or stack names that mark its
  resources in the cloud.

If the project or the deployment itself cannot be found, stop and tell me.
If you can find them but lack permission to read some of their details,
do not stop: keep going with what you can read and note what you could
not read. Do not guess or invent resources to fill the gaps.

Tell me in two or three lines what you are about to check (environment,
provider, regions, number of resources by type), then carry on without
waiting.

## Step 2. Compare

Read-only, compare every resource in the definition with what the cloud
provider reports now:
- **Missing**: defined in the deployment but not found.
- **Changed**: found, but an attribute differs from the definition: size
  or type, version, scaling, network or security group rules, encryption,
  public access, and tags (only if "Compare tags" is yes).
- **Unmanaged** (only if "Look for unmanaged resources" is yes): resources
  in the deployment's regions that carry its tags or belong to its stacks
  but are not in its definition.

For each finding, record the resource, the attribute, the expected value,
the actual value, and the evidence (resource id, API call, and UTC
timestamp).

Missing permissions never stop the check. If a resource or service cannot
be read (access denied, missing permission, service not enabled), keep
going with the rest, mark it "Not checked: <reason>" instead of reporting
it as missing or in sync, and list it in the notes with the missing
permission.

## Step 3. Rate

Rate each finding:
- High: drift that weakens security or availability, such as public
  access turned on, a port opened to the internet, encryption turned off,
  a missing load balancer or database, or scaling lowered.
- Medium: size, type, version, or scaling changes, and unmanaged
  resources.
- Low: tag differences and cosmetic settings.

For each, suggest a fix in one line: either update the deployment
definition to match (if the change looks intended) or revert the resource
(if it does not).

Set the overall status: Red if there is any High finding, Amber if there
are Medium findings only, Green if there are none or only Low. Say in the
notes that the status covers only what was checked.

## Step 4. Write the report

Write it in Markdown:
- `# <Project> Drift Report`, the deployment and environment, the UTC
  time of the check, and the overall status in bold.
- A summary: the number of resources checked, in sync, missing, changed,
  unmanaged, and not checked, and three to five bullets with what matters
  most.
- One `##` section each for High, Medium, and Low findings, as tables with
  the columns Resource, Attribute, Expected, Actual, Suggested fix, and
  Evidence. An empty section prints "No findings."
- `## Resources in sync`: a compact table of resources that match.
- `## Notes`: resources or services not checked and the permission that
  would allow them.

Save it as `drift-report-<project-slug>-<deployment-slug>-<YYYY-MM-DD>.md`.
Do not produce a PDF.

## Step 5. Tell me

In the chat, give me the overall status, the counts, and the High findings
in a few lines, then attach or link the Markdown report.

Change nothing: no resource, setting, tag, or definition may be created,
modified, or deleted, even where the report suggests a fix. If I ask you
afterwards to apply a fix, confirm it with me first.
```
