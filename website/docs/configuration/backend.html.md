---
layout: "language"
page_title: "Backend Configuration - Configuration Language"
---

# Backend Configuration

-> **Note:** This page is about Terraform 0.12 and later. For Terraform 0.11 and
earlier, see
[0.11 Configuration Language: Terraform Settings](../configuration-0-11/terraform.html).


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

## Using a Backend Block

Backends are configured with a nested `backend` block within the top-level
`terraform` block:

```hcl
terraform {
  backend "remote" {
    organization = "example_corp"

    workspaces {
      name = "my-app-prod"
    }
  }
}
```

There are some important limitations on backend configuration:

- A configuration can only provide one backend block.
- A backend block cannot refer to named values (like input variables, locals, or data source attributes).

For security or flexibility, you can omit some backend settings from your primary
configuration and provide them at run time. See
[Partial Configuration](#partial-configuration) below for details.

### Backend Types

The block label of the backend block (`"remote"`, in the example above) indicates which backend type to use. Terraform has a built-in selection of backends, and the configured backend must be available in the version of Terraform you are using.

The arguments used in the block's body are specific to the chosen backend type; they configure where and how the backend will store the configuration's state, and in some cases configure other behavior.

See the available backend types in the navigation sidebar for details about each supported backend type and its configuration arguments.

-> **Note:** Some backends allow providing access credentials directly in the configuration. This can be pragmatic in certain unusual situations, but for normal use we _do not_ recommend including access credentials as part of the backend configuration. Instead, leave those arguments completely unset and provide credentials via the credentials files or environment variables that are conventional for the target system, as described in the documentation for each backend.

### Default Backend

If a configuration includes no backend block, Terraform defaults to using the `local` backend, which performs operations on the local system and stores state as a plain file in the current working directory.

## Initialization

Terraform sets up the configured backend when initializing a working directory.
Terraform CLI uses the `terraform init` command for initializing; Terraform
Cloud and Terraform Enterprise handle initialization automatically.

## Changing Configuration

You can change your backend configuration at any time. You can change
both the configuration itself as well as the type of backend (for example
from `consul` to `s3`). Removing the backend configuration block is equivalent
to changing the backend type to `local`.

Terraform will automatically detect any changes in your configuration
and request a reinitialization. As part of
the reinitialization process, Terraform will ask if you'd like to migrate
your existing state to the new configuration. This allows you to easily
switch from one backend to another.

If you're using multiple [workspaces](/docs/state/workspaces.html),
Terraform can copy all workspaces to the destination. If Terraform detects
you have multiple workspaces, it will ask if this is what you want to do.

If you're just reconfiguring the same backend, Terraform will still ask if you
want to migrate your state. You can respond "no" in this scenario.

## Partial Configuration

You do not need to specify every required argument in the backend configuration.
Omitting certain arguments may be desirable if some arguments are provided
automatically by an automation script running Terraform. When some or all of
the arguments are omitted, we call this a _partial configuration_.

With a partial configuration, the remaining configuration arguments must be
provided as part of the initialization process.
There are several ways to supply the remaining arguments:

  * **File**: A configuration file may be specified via the `init` command line.
    To specify a file, use the `-backend-config=PATH` option when running
    `terraform init`. If the file contains secrets it may be kept in
    a secure data store, such as
    [Vault](https://www.vaultproject.io/), in which case it must be downloaded
    to the local disk before running Terraform.

  * **Command-line key/value pairs**: Key/value pairs can be specified via the
    `init` command line. Note that many shells retain command-line flags in a
    history file, so this isn't recommended for secrets. To specify a single
    key/value pair, use the `-backend-config="KEY=VALUE"` option when running
    `terraform init`.

  * **Interactively**: Terraform will interactively ask you for the required
    values, unless interactive input is disabled. Terraform will not prompt for
    optional values.

If backend settings are provided in multiple locations, the top-level
settings are merged such that any command-line options override the settings
in the main configuration and then the command-line options are processed
in order, with later options overriding values set by earlier options.

The final, merged configuration is stored on disk in the `.terraform`
directory, which should be ignored from version control. This means that
sensitive information can be omitted from version control, but it will be
present in plain text on local disk when running Terraform.

When using partial configuration, Terraform requires at a minimum that
an empty backend configuration is specified in one of the root Terraform
configuration files, to specify the backend type. For example:

```hcl
terraform {
  backend "consul" {}
}
```

A backend configuration file has the contents of the `backend` block as
top-level attributes, without the need to wrap it in another `terraform`
or `backend` block:

```hcl
address = "demo.consul.io"
path    = "example_app/terraform_state"
scheme  = "https"
```

The same settings can alternatively be specified on the command line as
follows:

```
$ terraform init \
    -backend-config="address=demo.consul.io" \
    -backend-config="path=example_app/terraform_state" \
    -backend-config="scheme=https"
```

The Consul backend also requires a Consul access token. Per the recommendation
above of omitting credentials from the configuration and using other mechanisms,
the Consul token would be provided by setting either the `CONSUL_HTTP_TOKEN`
or `CONSUL_HTTP_AUTH` environment variables. See the documentation of your
chosen backend to learn how to provide credentials to it outside of its main
configuration.


## More Details About Backends

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

