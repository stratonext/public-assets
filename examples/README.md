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
  suggested fixes, as Markdown and PDF.
- [cost-savings-digest](cost-savings-digest/prompt.md): downloads a
  project's deployment and creates a task template for it that reports the
  deployment's spend over the last 7 days and concrete savings with an
  estimated monthly figure, as Markdown and PDF.

Every prompt only reads: nothing changes a resource, even where a report
suggests a fix. Missing permissions never stop a run; the
report covers the services it can read and lists the rest.
