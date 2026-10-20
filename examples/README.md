# Examples

Prompts to paste into an agent chat that has the StratoNext skill
installed. Each one has the agent read your own StratoNext resources and
work from them. Some create a template tailored to your deployment, to run
as a task and schedule; others run once, right in the chat.

- [daily-ops-journal](daily-ops-journal/prompt.md): downloads a project's
  deployment and creates a task template for it that writes a
  newspaper-style ops journal of the deployment's last 24 hours, as
  Markdown and PDF.
- [drift-report](drift-report/prompt.md) (one-time): downloads a
  project's deployment, compares what it defines with what is running in
  the cloud, and reports missing, changed, and unmanaged resources with
  suggested fixes, as a Markdown report.
- [cost-savings-digest](cost-savings-digest/prompt.md): downloads a
  project's deployment and creates a task template for it that reports the
  deployment's spend over the last 7 days and concrete savings with an
  estimated monthly figure, as Markdown and PDF.
- [autopilot](autopilot/prompt.md): hands an application's monitoring and
  maintenance to StratoNext end-to-end. Plans and, once you approve,
  creates a daily controller that checks every deployment and starts
  investigations or fixes when needed, scheduled reports, a monthly
  review, and project notes it uses as memory, improving its own
  templates over time within fixed guardrails.

The report and check prompts only read: they never change a resource,
even where a report suggests a fix. The autopilot only changes resources
as its autonomy setting allows, and never deletes data. Missing
permissions never stop a run; each covers the services it can read and
lists the rest.
