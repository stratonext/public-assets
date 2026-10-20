# AutoOps: hand an application's monitoring and maintenance to StratoNext

Paste the prompt below into an agent chat that has the StratoNext skill
installed. Fill in the **Inputs** block first.

The agent studies the project and its deployments, then designs and sets
up a complete operating system for them in StratoNext: templates, tasks,
schedules, and project notes. Once it is running:
- **Every day** a controller task checks every deployment, triages what it
  finds, and starts new work when something needs attention.
- **Anything critical** gets an investigation started immediately.
- **Reports** (a daily ops journal, a weekly cost and savings digest, a
  weekly drift and security check) run on their own schedules.
- **It improves itself**: each run records what it learned in the project
  notes and tunes its own templates, within guardrails it can never
  loosen.

Nothing is created until you approve the plan. Pausing it is one step:
disable its schedules.

---

```text
Take over the monitoring and maintenance of one of my applications,
end-to-end, using StratoNext. Study the project and its deployments, plan
a complete operating setup, show me the plan, and once I approve it,
create everything.

## Inputs

- Project: <project name or id>
- Deployments: <one or more deployment names or ids, or "all">
- Autonomy: propose-fixes
  (report-only: never change anything;
   propose-fixes: prepare fixes and wait for my approval before applying;
   fix-low-risk: apply only the low-risk fixes listed in the plan on your
   own, everything else waits for my approval)
- Daily controller time (UTC): 06:00
- Maximum new tasks the controller may start per day: 5
- Who to notify about critical issues: <name, team, or channel, if
  StratoNext supports notifications>

## Step 1. Discover

Use the StratoNext skill to download and read:
- The project: name, description, owners or teams, existing project
  notes, and its deployments.
- Each selected deployment: environment, cloud provider, accounts,
  regions, every resource it defines with its key settings, how it is
  deployed (pipelines, stacks, repositories), what monitors it (alarms,
  dashboards, health checks, log groups), how it is backed up, and the
  tags or stack names that mark its resources.
- What already exists for this project in StratoNext: templates, tasks,
  schedules, and agents, so the plan reuses or replaces them instead of
  duplicating them.
- What the StratoNext skill lets you do: create and edit templates,
  create tasks, schedule tasks, start a task from inside another task,
  write project notes, notify people. The plan must only rely on what is
  actually available; for anything missing, plan a fallback and tell me.

If the project or a deployment cannot be found, stop and tell me. If you
lack permission to read some details or services, keep going with what
you can read and keep a list of the gaps and the permission each needs.
Do not invent resources.

Fetch the template format before designing anything:
- https://raw.githubusercontent.com/stratonext/public-assets/main/templates/stratonext-template-schema.yaml
- https://raw.githubusercontent.com/stratonext/public-assets/main/templates/aws/aws-region-cost-distribution-report.yaml

Every template you create must validate against that schema: only the
`environment`, `agent`, and `variables` placeholders, every `{{key}}` a
declared variable and every declared variable used. Bake each
deployment's real resources, alarms, pipelines, regions, and tags into
the instructions instead of asking for them.

## Step 2. Design the operating setup

Design the following for each selected deployment, named
`autoops-<project-slug>-<deployment-slug>-<role>`. Leave out anything the
deployment has nothing for, and say why.

### Project notes: the shared memory

Create (or extend, if they exist) project notes that every task reads at
the start and updates at the end:
- **Runbook**: how the application is built and deployed, its critical
  paths, what "healthy" means for it, and who owns what.
- **Baselines**: normal values for its key signals (error rates,
  latency, CPU, queue depth, daily spend), filled in and refined from
  real observations.
- **Known issues**: recurring problems, their signature, and the known
  fix or workaround.
- **Open work**: every task the setup has started and not finished,
  with its reason and status, so nothing is started twice.
- **AutoOps changelog**: every change the setup makes to its own
  templates, schedules, or notes structure, with the date, reason, and
  evidence.

### Controller (daily, at the controller time)

The heart of the setup: one task per deployment, every day. Each run:
1. Reads the project notes.
2. Runs the health checks: alarms and health checks firing, error and
   latency against the baselines, failed or restarted compute, failed
   pipeline runs, failed backups, capacity above 80%, new high-severity
   security findings, and resources added or removed outside the
   deployment's definition.
3. Triages every finding as critical, warning, or info:
   - Critical (service down or degraded, data at risk, security exposure,
     a failed backup of production data): immediately start an
     investigation task for it and notify the people in the inputs.
   - Warning: start a maintenance task if a known fix exists in the
     notes, otherwise add it to Open work for the next report.
   - Info: record it in the notes only.
   Before starting any task, check Open work: never start a second task
   for an issue that already has one open; update the open one instead.
   Never start more than the daily maximum of new tasks; if more are
   needed, start the most severe ones and list the rest in Open work.
4. Updates the notes: Baselines with the day's values, Known issues with
   any new recurring pattern, and Open work with what it started or
   closed.
5. Improves the setup (see "Self-improvement" below).
6. Ends with a short run summary: status per deployment, tasks started,
   and changes made to the setup.

### Investigation (started on demand by the controller)

A template the controller starts with the issue, its first evidence, and
the time it began. It gathers alarms, logs, metrics, audit-log changes,
and deployments around that time, builds a timeline, identifies the
most likely cause with evidence, assesses impact, and recommends a fix.
It writes its findings to the project notes (Known issues and Open work)
and publishes them as a Markdown artifact. It is read-only.

### Maintenance (started on demand by the controller)

A template the controller starts with the issue and the proposed fix. It
follows the Autonomy input:
- report-only: describes the fix and changes nothing.
- propose-fixes: prepares the exact change, publishes it for my
  approval, and applies it only after I approve.
- fix-low-risk: applies the fix on its own only if it is on the plan's
  low-risk list (for example restarting a failed task or container,
  re-running a failed backup or pipeline, clearing a full temporary
  disk); anything else waits for my approval.
Before applying any change, it records the current state so the change
can be rolled back, and afterwards it verifies the fix worked. It never
deletes data, resources, or backups, and never changes security,
networking, or permissions without my approval, whatever the autonomy.

### Reports (scheduled)

- **Daily ops journal** (after the controller): a newspaper-style
  journal of the last 24 hours in UTC, written in Markdown and converted
  to PDF with the Markdown-to-PDF tool: health, incidents, changes,
  cost, security, backups, with a lead story, an overall Green, Amber, or
  Red status, and action items taken from Open work.
- **Weekly cost and savings digest**: the deployment's spend over the
  last 7 days and ranked savings with estimated monthly USD, effort, and
  risk, as Markdown and PDF.
- **Weekly drift and security check**: what is running versus what the
  deployment defines, and high-severity security issues, as a Markdown
  report. New findings go into Open work for the controller.

### Monthly review (first day of each month)

Reviews the month in the project notes and the changelog: what was
caught, what was missed, which checks were noisy, which tasks were
started and how they ended. Proposes larger changes (new checks, new
reports, schedule changes, autonomy changes) to me for approval, and
writes a one-page monthly summary as Markdown and PDF.

### Self-improvement and its guardrails

Every controller run and every monthly review may improve the setup:
- Add checks for resources that appeared in the deployment's
  definition, and drop checks for resources that were removed.
- Tune thresholds against the Baselines when a check is consistently
  noisy or consistently late.
- Add a Known issues entry and a matching fast check when the same
  problem recurs.
- Edit its own templates to do the above, bumping each template's
  `version` and validating it against the schema before saving.

Guardrails that self-improvement can never change:
- Never raise its own autonomy, never weaken the rules on what
  Maintenance may change, and never remove the read-only rule from any
  report or investigation.
- Never delete a template, task, schedule, or note; disable or archive
  it instead, with the reason in the changelog.
- At most 3 template changes per day; every change goes in the
  AutoOps changelog with its reason and evidence, and is listed in the
  next daily journal.
- Changes to schedules, notification targets, or the low-risk list are
  proposals for me to approve, never applied directly.
- If a template change makes the next run fail, revert it to the
  previous version and record why.

### Missing permissions

Missing permissions never stop any task: each one works with the
services it can read, marks the rest "Not available: <reason>", and lists
the gaps in its output and in the project notes.

## Step 3. Recap the plan

Do not show me YAML. Give me a plain-language recap I can read in two
minutes:
- **What you found**: the project and each deployment in a line or two
  (provider, regions, main resources, monitoring, pipelines, backups).
- **The setup**: a table with one row per template and task (name, what
  it does, schedule or trigger, which deployments).
- **A typical day**: what runs when, in UTC.
- **What happens when something breaks**: the path from a critical
  finding to an investigation, a notification, and a fix, under the
  chosen autonomy.
- **Low-risk fixes**: the exact list Maintenance may apply on its own
  (only under fix-low-risk).
- **How it improves itself** and the guardrails, in a few bullets.
- **Not covered yet**: gaps from missing permissions or missing
  StratoNext capabilities, and what would close each.
- **How to pause or undo**: which schedules to disable, and where the
  changelog is.

Wait for my approval. If I ask for changes, update the plan and recap
again.

## Step 4. Create

Once I approve:
1. Create or extend the project notes.
2. Create every template, validating each against the schema first.
3. Create the tasks from the templates for each deployment, with the
   right environment and agent.
4. Create the schedules.
5. Run the controller once now for each deployment as a first check,
   and fill the Baselines from what it sees.

Then verify that every template, task, schedule, and note in the plan
exists, and give me a final recap: what was created (with names), what
the first controller run found, what is not covered yet, and how to
pause it. If anything could not be created, say what and why, and how to
finish it by hand.
```
