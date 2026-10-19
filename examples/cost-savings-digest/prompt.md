# Cost and savings digest: template generator prompt

Paste the prompt below into an agent chat that has the StratoNext skill
installed. Fill in the two values in the **Inputs** block first.

The agent downloads the project and deployment definitions, then creates a
StratoNext task template tailored to that deployment. The task the template
creates reports what the deployment's own resources cost over the last 7
days and lists concrete savings with an estimated monthly figure, as
Markdown and as PDF (via the agent's Markdown-to-PDF tool).

Run it once per deployment you want a digest for. To get one regularly,
for example weekly, schedule the task created from the template in
StratoNext.

---

```text
Create a StratoNext task template that writes a cost and savings digest
for one deployment of one of my projects.

## Inputs

- Project: <project name or id>
- Deployment: <deployment name or id>

## Step 1. Download the project and deployment

Use the StratoNext skill to download:
- The project: its name, description, owners or teams, and its list of
  deployments.
- The deployment: its environment, cloud provider, accounts or
  subscriptions, regions, every resource and service it defines with its
  size or tier (compute, containers, functions, databases, storage,
  caches, queues, load balancers, NAT gateways, data transfer paths), its
  expected traffic or scaling settings if defined, and the tags or stack
  names that mark its resources so their cost can be told apart from
  other workloads in the same account.

If the project or the deployment itself cannot be found, stop and tell me.
If you can find them but lack permission to read some of their details or
services (for example billing or utilization metrics), do not stop: keep
going with what you can read, and keep a list of what you could not read
and which permission was missing. Do not guess or invent resources to
fill the gaps.

Show me a short summary of what you found (environment, provider, regions,
the resources by type and size, how their cost can be attributed), plus
the list of what you could not read, and wait for me to confirm before
going on.

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
- `id`: `<project-slug>-<deployment-slug>-cost-savings-digest`
- `name`: `<Project> <deployment> cost and savings digest`
- `category`: `cost-optimization`, `version`: 1, and tags including
  `cost-optimization` and `savings`.
- `placeholders.environment`: the environment the deployment runs in,
  required. `placeholders.agent`: the assignee, required.
- Exactly these variables, for what a user would reasonably change:
  `min_monthly_savings_usd`, the minimum estimated monthly saving to
  report (number, in USD, default 20), and `underused_cpu_percent`, the
  average CPU utilization below which a resource counts as underused
  (number, in percent, default 10). Use them in the instructions as
  `{{min_monthly_savings_usd}}` and `{{underused_cpu_percent}}`. Everything that comes from the deployment is
  written into the instructions, not asked for.

The task `instructions` must tell the agent that runs it to do all of the
following. Write them for this deployment specifically: name its real
regions, accounts, resources, sizes, and the tags or stack names used to
attribute cost, and only include savings checks for resource types the
deployment actually has.

1. **Window.** Cover exactly the last 7 days, ending now, with every date
   and time in UTC. State the window at the top of the digest. The digest
   stands on its own: never read, compare with, or mention earlier
   digests, reports, or periods.

2. **Spend**, read-only, from the provider's billing data, filtered to the
   deployment's resources by their tags or stacks:
   - Total spend in the window, and the projected monthly spend at that
     rate.
   - Spend by service and by resource, with the top 10 resources.
   - Spend that could not be attributed to a resource (shared or untagged
     cost), stated separately rather than guessed.
   - Say so if billing data for the end of the window is not complete
     yet.

3. **Savings**: check only the resource types this deployment has, for
   example:
   - Compute or databases whose average CPU over the window is below
     {{underused_cpu_percent}}%, with a smaller size suggested.
   - Previous-generation instance families or tiers with a cheaper
     current equivalent.
   - Storage that is unattached, over-provisioned, or on a more expensive
     class than its access pattern needs; snapshots and backups beyond
     the deployment's retention.
   - Steady usage not covered by reserved capacity or savings plans.
   - Data transfer or NAT charges a private endpoint could reduce.
   - Load balancers, IPs, or other resources with no traffic in the
     window.
   For each saving: the resource, the evidence (metric values or billing
   figures, with UTC timestamps), the suggested change, the estimated
   monthly saving in USD, the effort (low, medium, high), and the risk.
   Leave out any saving below {{min_monthly_savings_usd}} USD a month.

   Missing permissions never stop the digest. If billing data, metrics,
   or a service cannot be read (access denied, missing permission,
   service not enabled), keep going with what is available and still
   publish the digest. List every source that could not be read, with the
   error or missing permission, in the notes, and print "Not available:
   <reason>" in the affected section instead of guessing figures.

4. **Write the Markdown digest**:
   - `# <Project> Cost & Savings Digest`, the deployment and environment,
     the UTC window, the total spend, the projected monthly spend, and the
     total estimated monthly savings in bold.
   - A summary: three to five bullets with what drives the cost and the
     biggest savings.
   - `## Spend`: tables of spend by service and the top 10 resources,
     and the unattributed spend.
   - `## Savings`: a table ranked by estimated monthly saving, with the
     columns Resource, Change, Monthly saving (USD), Effort, Risk, and
     Evidence. An empty section prints "No savings above
     {{min_monthly_savings_usd}} USD a month found."
   - `## Notes`: sources that were unavailable and assumptions behind the
     estimates (prices used, hours per month).
   - Page breaks in the syntax the Markdown-to-PDF tool supports, so the
     summary stands alone on page 1.

5. **Convert to PDF** with the Markdown-to-PDF tool, without any separate
   layout, so the PDF and Markdown carry the same content. Open the PDF
   and check that every section is present and tables are readable.

6. **Publish** both as workspace artifacts named
   `cost-digest-<project-slug>-<deployment-slug>-<YYYY-MM-DD>.md` and
   `.pdf`, using the window's end date.

7. Change nothing: no resource, size, setting, or commitment may be
   created, modified, purchased, or deleted, even where the digest
   suggests it.

Also write `verificationCriteria` that checks: both artifacts exist with
the same content; the window is the last 7 days in UTC and there is no
reference to earlier periods or reports; spend is limited to the
deployment's resources with unattributed spend shown separately; every
saving has evidence, an estimated monthly figure of at least
{{min_monthly_savings_usd}} USD, an effort, and a risk; every source that
could not be read is listed in the notes with its reason; and nothing was
changed. A digest built from only some sources because of missing
permissions still passes.

## Step 4. Validate and create

Validate the template against the schema and fix any error. Check that
every resource, size, region, and tag in the instructions comes from what
you downloaded in step 1. Parts of the deployment you could not read in
step 1 still get their checks in the instructions, described generically,
so the digest covers them once permissions are granted.

Then, instead of showing me the YAML, give me a short, plain-language
recap of the template you are about to create:
- **Name** and what it does, in one sentence.
- **Covers**: the project, deployment, environment, cloud provider, and
  regions, and how cost is attributed to the deployment (which tags or
  stacks).
- **Savings it will look for**: one line per check, naming the resources
  it applies to (for example "Underused compute: 6 ECS services and 2
  RDS instances").
- **Not covered yet**: anything you could not read because of missing
  permissions, and the permission that would add it.
- **Options I can set** when creating a task from it, with their
  defaults (minimum monthly saving, underused CPU threshold).
- **Output**: the PDF and Markdown file names, and that the digest
  covers the last 7 days in UTC and changes nothing.

Keep it short enough to read at a glance. Show the YAML only if I ask for
it. Once I approve the recap, create the template in my StratoNext
workspace with the StratoNext skill. Tell me its name and that I can now
create a task from it and schedule that task, for example weekly.
```
