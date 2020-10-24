---
layout: "language"
page_title: "Backend Overview - Configuration Language"
---

Each Terraform configuration can specify a backend, which defines where
and how operations are performed, where [state](/docs/state/index.html)
snapshots are stored, etc.

- If you are just teaching yourself Terraform, we recommend using the default
  `local` backend, which requires no configuration.
- If you and your team are using Terraform to manage meaningful infrastructure,
  we recommend using the `remote` backend with [Terraform Cloud](/docs/cloud/index.html)
  or [Terraform Enterprise](/docs/enterprise/index.html).

## Where Backends are Used

Backend configuration is only used by [Terraform CLI](/docs/cli-index.html).

When Terraform Cloud and Terraform Enterprise perform Terraform runs, they
always use their own state storage for the current workspace, ignoring any
backend block in the configuration.

However, we still recommend including a backend block in configurations intended
for use with Terraform Cloud. When the `remote` backend is properly configured,
you can use Terraform CLI in your local terminal to perform remote runs in
Terraform Cloud, which enables faster development workflows.

## What Backends Do

There are two areas of Terraform's behavior that are determined by the backend:

- Where state is stored.
- Where operations are performed.

### State

Terraform uses persistent [state](/docs/state/index.html) data to keep track of
the resources it manages. Since it needs the state in order to know which
real-world infrastructure objects correspond to the resources in a
configuration, everyone working with a given collection of infrastructure
resources must be able to access the same state data.

The `local` backend stores state as a local file on disk, but every other
backend stores state in a remote service of some kind, which allows multiple
people to access it. Accessing state in a remote service generally requires some
kind of access credentials, since state date contains extremely sensitive
information.

Some backends act like plain "remote disks" for state files; others support
_locking_ the state while operations are being performed, which helps prevent
conflicts and inconsistencies.

### Operations

"Operations" refers to performing API requests against infrastructure services
in order to create, read, update, or destroy resources. Not every `terraform`
subcommand performs API operations; many of them only operate on state data.

Only two backends actually perform operations: `local` and `remote`.

The `local` backend performs API operations directly from the machine where the
`terraform` command is run. Whenever you use a backend other than `local` or
`remote`, Terraform uses the `local` backend for operations; it only uses the
configured backend for state storage.

The `remote` backend can perform API operations remotely, using Terraform Cloud
or Terraform Enterprise. When running remote operations, the local `terraform`
command displays the output of the remote actions as though they were being
performed locally, but only the remote system requires cloud credentials or
network access to the resources being managed.

Remote operations are optional for the `remote` backend; the settings for the
target Terraform Cloud workspace determine whether operations run remotely or
locally. If local operations are configured, Terraform uses the `remote` backend
for state and the `local` backend for operations, like with the other state
backends.
