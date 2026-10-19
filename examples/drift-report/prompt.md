# Drift report: template generator prompt

Paste the prompt below into an agent chat that has the StratoNext skill
installed. Fill in the two values in the **Inputs** block first.

The agent downloads the project and deployment definitions, then creates a
StratoNext task template tailored to that deployment. The task the template
creates compares what the deployment defines with what is actually running
in the cloud, and reports every resource that is missing, unmanaged, or
configured differently, as Markdown and as PDF (via the agent's
Markdown-to-PDF tool).

Run it once per deployment you want to check. To check regularly, for
example weekly, schedule the task created from the template in StratoNext.

---

```text
Create a StratoNext task template that reports configuration drift for one
deployment of one of my projects: where what is running in the cloud no
longer matches what the deployment defines.

## Inputs

- Project: <project name or id>
- Deployment: <deployment name or id>

## Step 1. Download the project and deployment

Use the StratoNext skill to download:
- The project: its name, description, owners or teams, and its list of
  deployments.
- The deployment: its environment, cloud provider, accounts or
  subscriptions, regions, and the full definition of every resource it
  declares (type, identifier or name, size or instance type, version,
  scaling settings, network and security group rules, encryption, public
  access settings, tags), how it is deployed (pipelines, stacks,
  repositories), and the tags or stack names that mark its resources in
  the cloud.

If the project or the deployment itself cannot be found, stop and tell me.
If you can find them but lack permission to read some of their details or
services, do not stop: keep going with what you can read, and keep a list
of what you could not read and which permission was missing. Do not guess
or invent resources to fill the gaps.

Show me a short summary of what you found (environment, provider, regions,
the resources by type, how its resources are tagged or grouped), plus the
list of what you could not read, and wait for me to confirm before going
on.

## Step 2. Read the template format

Fetch the template schema and an example template:
- https://raw.githubusercontent.com/stratonext/public-assets/main/templates/stratonext-template-schema.yaml
- https://raw.githubusercontent.com/stratonext/public-assets/main/templates/aws/aws-region-cost-distribution-report.yaml

The template you write must validate against that schema. In particular:
- `placeholders` may only contain `environment`, `agent`, and `variables`.
  Do not invent other placeholders.
- Every `{{key}}` used in the `task` fields must be a declared variable,
  and every declared variable must be used.
- Variable types are only those the schema allows; use the native
  `aws-region` / `aws-account-id` types where they fit.
- `cloudProvider` is the deployment's provider, from the schema's enum.

## Step 3. Write the template

Write a template with:
- `id`: `<project-slug>-<deployment-slug>-drift-report`
- `name`: `<Project> <deployment> drift report`
- `category`: `checkup`, `version`: 1, and tags including `drift` and
  `compliance`.
- `placeholders.environment`: the environment the deployment runs in,
  required. `placeholders.agent`: the assignee, required.
- Exactly these variables, for what a user would reasonably change:
  `check_tags`, whether to compare tags (boolean, default true), and
  `check_unmanaged`, whether to look for unmanaged resources that carry
  the deployment's tags but are not in its definition (boolean, default
  true). Use them in the instructions as `{{check_tags}}` and
  `{{check_unmanaged}}`. Everything that comes from the deployment is written into
  the instructions, not asked for.

The task `instructions` must tell the agent that runs it to do all of the
following. Write them for this deployment specifically: embed the expected
configuration of every resource the deployment defines (identifier, type,
and the attributes listed in step 1), its regions and accounts, and the
tags or stack names that identify its resources.

1. **Snapshot.** Report the state at the moment the task runs, with the
   time in UTC at the top of the report. The report stands on its own:
   never read, compare with, or mention earlier reports.

2. **Compare**, read-only, every resource in the expected configuration
   with what the cloud provider reports now:
   - **Missing**: defined in the deployment but not found.
   - **Changed**: found, but an attribute differs from the definition
     (size or type, version, scaling, network or security group rules,
     encryption, public access, and tags; compare tags only when tag
     checking is turned on: {{check_tags}}).
   - **Unmanaged**: resources in the deployment's
     regions that carry its tags or belong to its stacks but are not in
     its definition. Look for these only when the unmanaged check is
     turned on: {{check_unmanaged}}.
   For each finding, record the resource, the attribute, the expected
   value, the actual value, and the evidence (resource id, API call, and
   UTC timestamp).

3. **Rate** each finding:
   - High: drift that weakens security or availability, such as public
     access turned on, a port opened to the internet, encryption turned
     off, a missing load balancer or database, or scaling lowered.
   - Medium: size, type, version, or scaling changes, and unmanaged
     resources.
   - Low: tag differences and cosmetic settings.
   For each, suggest a fix in one line: either update the deployment
   definition to match (if the change looks intended) or revert the
   resource (if it does not). Do not apply it.

   Set the overall status: Red if there is any High finding, Amber if
   there are Medium findings only, Green if there are none or only Low.

   Missing permissions never stop the report. If a resource or service
   cannot be read (access denied, missing permission, service not
   enabled), keep going with the rest and still publish the report. List
   every resource or service that could not be checked, with the error or
   missing permission, in the notes, and mark it "Not checked: <reason>"
   instead of reporting it as missing or in sync. Say in the notes that
   the status covers only what was checked.

4. **Write the Markdown report**:
   - `# <Project> Drift Report`, the deployment and environment, the UTC
     snapshot time, and the overall status in bold.
   - A summary: the number of resources checked, in sync, missing,
     changed, unmanaged, and not checked, and three to five bullets with
     what matters most.
   - One `##` section each for High, Medium, and Low findings, as tables
     with the columns Resource, Attribute, Expected, Actual, Suggested
     fix, and Evidence. An empty section prints "No findings."
   - `## Resources in sync`: a compact table of resources that match.
   - `## Notes`: resources or services not checked and why.
   - Page breaks in the syntax the Markdown-to-PDF tool supports, so the
     summary stands alone on page 1.

5. **Convert to PDF** with the Markdown-to-PDF tool, without any separate
   layout, so the PDF and Markdown carry the same content. Open the PDF
   and check that every section is present and tables are readable.

6. **Publish** both as workspace artifacts named
   `drift-report-<project-slug>-<deployment-slug>-<YYYY-MM-DD>.md` and
   `.pdf`, using the snapshot date.

7. Change nothing: no resource, setting, tag, or definition may be
   created, modified, or deleted, even where the report suggests a fix.

Also write `verificationCriteria` that checks: both artifacts exist with
the same content; every resource in the deployment's definition appears
in the report as in sync, missing, changed, or not checked; every finding
has expected and actual values, a severity, a suggested fix, and
evidence; there is no reference to earlier reports; every resource or
service that could not be checked is listed in the notes with its reason;
and nothing was changed. A report built from only some services because
of missing permissions still passes.

## Step 4. Validate and create

Validate the template against the schema and fix any error. Check that
every resource, attribute value, region, and tag in the instructions comes
from what you downloaded in step 1. Parts of the deployment you could not
read in step 1 are still listed in the instructions, so the report marks
them "Not checked" until permissions are granted.

Then, instead of showing me the YAML, give me a short, plain-language
recap of the template you are about to create:
- **Name** and what it does, in one sentence.
- **Covers**: the project, deployment, environment, cloud provider, and
  regions.
- **What it will check**: how many resources by type, and which
  attributes for each (for example "4 databases: instance class, engine
  version, encryption, public access").
- **Not covered yet**: anything you could not read because of missing
  permissions, and the permission that would add it.
- **Options I can set** when creating a task from it, with their
  defaults (tag check, unmanaged resources check).
- **Output**: the PDF and Markdown file names, and that the report is a
  snapshot of the moment it runs and changes nothing.

Keep it short enough to read at a glance. Show the YAML only if I ask for
it. Once I approve the recap, create the template in my StratoNext
workspace with the StratoNext skill. Tell me its name and that I can now
create a task from it and schedule that task, for example weekly.
```
