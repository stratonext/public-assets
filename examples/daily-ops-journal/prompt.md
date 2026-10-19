# Daily ops journal: template generator prompt

Paste the prompt below into an agent chat that has the StratoNext skill
installed. Fill in the two values in the **Inputs** block first.

The agent downloads the project and deployment definitions, then creates a
StratoNext task template tailored to that deployment. The task the template
creates writes a newspaper-style ops journal of the deployment's last 24
hours, as Markdown and as PDF (via the agent's Markdown-to-PDF tool).

Run it once per deployment you want a journal for. To get a journal every
day, schedule the task created from the template in StratoNext.

---

```text
Create a StratoNext task template that writes a daily ops journal tailored
to one deployment of one of my projects.

## Inputs

- Project: <project name or id>
- Deployment: <deployment name or id>

## Step 1. Download the project and deployment

Use the StratoNext skill to download:
- The project: its name, description, owners or teams, and its list of
  deployments.
- The deployment: its environment, cloud provider, accounts or
  subscriptions, regions, and every resource and service it defines
  (compute, containers, functions, databases, storage, queues, load
  balancers, networking, DNS), how it is deployed (pipelines, stacks,
  repositories), what monitors it (alarms, dashboards, health checks, log
  groups), how it is backed up, and any tags it uses to mark its
  resources.

If the project or deployment cannot be found, or the skill has no way to
download one of them, stop and tell me what is missing. Do not guess or
invent resources.

Show me a short summary of what you found (environment, provider, regions,
the main resources by type, monitoring, pipelines, backups) and wait for
me to confirm before going on.

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
- `id`: `<project-slug>-<deployment-slug>-daily-ops-journal`
- `name`: `<Project> <deployment> daily ops journal`
- `category`: `operations`, `version`: 1, and tags including `operations`
  and `journal`.
- `placeholders.environment`: the environment the deployment runs in,
  required. `placeholders.agent`: the assignee, required.
- Variables only for what a user would reasonably change: whether to
  include the cost section (boolean, default true), whether to include the
  security section (boolean, default true), and the minimum security
  severity to report (string, default HIGH). Everything that comes from the
  deployment is written into the instructions, not asked for.

The task `instructions` must tell the agent that runs it to do all of the
following. Write them for this deployment specifically: name its real
regions, accounts, resources, alarms, log groups, pipelines, backup plans,
and tags wherever an instruction refers to them, and leave out any beat
the deployment has nothing for (for example, no backups section if it
defines no backups).

1. **Window.** Cover exactly the last 24 hours, ending now, with every date
   and time in UTC. State the window at the top of the journal. Every
   figure, event, and finding must fall in that window. The journal stands
   on its own: never read, compare with, or mention earlier journals or
   reports.

2. **Gather**, read-only, from the deployment's own resources:
   - Health: which of its alarms and health checks fired, for how long,
     and whether they are still firing; unhealthy targets; instances,
     containers, or functions that restarted, failed, or were replaced.
   - Incidents: anything that degraded or interrupted service, with
     start and end time, impact, cause, and status.
   - Changes and deployments: changes to its resources from the
     provider's audit log, grouped by who made them; its pipeline or stack
     runs and whether they succeeded; changes made outside its usual
     automation.
   - Cost (when the cost variable is on): total spend of the deployment's
     resources in the window and its top 5 services, with no comparison
     to any earlier period; say so if billing data for the window is not
     complete yet.
   - Security (when the security variable is on): findings at or above
     the minimum severity, raised or updated in the window, on its
     resources.
   - Backups and capacity: its backup jobs that failed or did not run;
     its quotas, disks, or databases above 80% of capacity.
   If a data source is unavailable, say so in the journal's notes instead
   of skipping it.

3. **Curate** it into a newspaper edition in the style of the Wall Street
   Journal:
   - A lead story: the most important event, in this order: an unresolved
     incident, a critical security finding, a resolved incident, a failed
     or risky change, a failed backup. If nothing qualifies, an "all
     clear" lead with the window's key figures.
   - One to three secondary stories, an at-a-glance table of key figures,
     security findings, action items raised by the window's events, and
     optional one-line briefs.
   - Original 2-4 sentence stories, each ending with a source line of
     evidence (resource id, alarm name, audit-log event, or figure, with a
     UTC timestamp). Nothing without evidence; no filler. An empty
     section prints one honest line, such as "No incidents in this
     period."
   - An overall status: Red for an unresolved incident or a critical
     security finding; Amber for a resolved incident, a firing alarm, or a
     failed backup; Green otherwise.

4. **Write the Markdown edition**, laid out as:
   - Front page: `# <Project> Ops Daily`, the deployment and environment
     as a tagline in italics, a dateline (date, UTC window, and bold
     status) between two horizontal rules, the lead (uppercase bold
     section label, `##` headline, italic dek, byline naming the data
     sources, body, italic source line), the at-a-glance table, action
     items, secondary stories and security as `###` stories, and an
     "Inside" list of the sections that follow.
   - Inside sections, each starting on a new page: one `##` section per
     beat that applies to this deployment, with `###` stories and tables
     for lists.
   - Back page, on a new page: the action items table (Item, Owner,
     Raised by), the briefs, and the notes.
   - Page breaks in the syntax the Markdown-to-PDF tool supports.

5. **Convert to PDF** with the Markdown-to-PDF tool, without any separate
   layout, so the PDF and Markdown carry the same content. Open the PDF
   and check that every section is present and tables are readable.

6. **Publish** both as workspace artifacts named
   `ops-journal-<project-slug>-<deployment-slug>-<YYYY-MM-DD>.md` and
   `.pdf`, using the window's end date.

7. Change nothing: no resource, setting, or alarm may be created,
   modified, deleted, acknowledged, or silenced.

Also write `verificationCriteria` that checks: both artifacts exist with
the same content; the window is the last 24 hours in UTC and nothing falls
outside it; there is no reference to earlier journals; the front page,
inside sections, and back page are present; every story has a source
line; and nothing was changed.

## Step 4. Validate and create

Validate the template against the schema and fix any error. Check that
every resource, alarm, pipeline, and region named in the instructions
comes from what you downloaded in step 1.

Then show me the full template YAML and, once I approve it, create it in
my StratoNext workspace with the StratoNext skill. Tell me its id and that
I can now create a task from it and schedule that task to run daily.
```
