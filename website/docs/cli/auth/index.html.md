---
layout: "docs"
page_title: "Authentication - Terraform CLI"
---

# CLI Authentication

[Terraform Cloud](/docs/cloud/index.html) and
[Terraform Enterprise](/docs/enterprise/index.html) are platforms that perform
Terraform runs to provision infrastructure, offering a collaboration-focused
environment that makes it easier for teams to use Terraform together. (For
expediency, the content below refers to both products as "Terraform Cloud.")

Terraform CLI can integrate with Terraform Cloud in several ways:

- Using [the `remote` backend](/docs/backends/types/remote.html), you can
  configure Terraform CLI to act as a frontend for performing runs in a
  Terraform Cloud workspace. This lets you run Terraform plans interactively in
  your local terminal while you test changes to a configuration, but perform API
  operations from Terraform Cloud's remote infrastructure using credentials
  configured in the remote workspace. See
  [the Terraform Cloud docs on CLI-driven runs](/docs/cloud/run/cli.html) for
  details.
- Terraform Cloud workspaces can optionally be configured for local runs with
  Terraform CLI, which then treats the remote workspace as a plain state
  backend. This also uses the `remote` backend.
- Terraform CLI can install and use modules from Terraform Cloud's private
  module registry, even for configurations that are not associated with a
  Terraform Cloud workspace.

In order to control access to your organization's information and
infrastructure, all of these use cases require you to authenticate Terraform CLI
with your Terraform Cloud account.

The best way to handle CLI authentication is with the `login` and `logout`
commands, which help automate the process of getting an API token for your
Terraform Cloud user account.

For details, see:

- [The `terraform login` command](/docs/commands/login.html)
- [The `terraform logout` command](/docs/commands/logout.html)
