---
layout: "language"
page_title: "Resources - Configuration Language"
sidebar_current: "docs-config-resources"
description: |-
  Resources are the most important element in a Terraform configuration.
  Each resource corresponds to an infrastructure object, such as a virtual
  network or compute instance.
---

# Resources

-> **Note:** This page is about Terraform 0.12 and later. For Terraform 0.11 and
earlier, see
[0.11 Configuration Language: Resources](../configuration-0-11/resources.html).

> **Hands-on:** Try the [Terraform: Get Started](https://learn.hashicorp.com/collections/terraform/aws-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS) collection on HashiCorp Learn.

_Resources_ are the most important element in the Terraform language.
Each resource block describes one or more infrastructure objects, such
as virtual networks, compute instances, or higher-level components such
as DNS records.

## Resource Syntax

Resource declarations can include a number of advanced features, but only
a small subset are required for initial use. More advanced syntax features,
such as single resource declarations that produce multiple similar remote
objects, are described later in this page.

```hcl
resource "aws_instance" "web" {
  ami           = "ami-a1b2c3d4"
  instance_type = "t2.micro"
}
```

A `resource` block declares a resource of a given type ("aws_instance")
with a given local name ("web"). The name is used to refer to this resource
from elsewhere in the same Terraform module, but has no significance outside
that module's scope.

The resource type and name together serve as an identifier for a given
resource and so must be unique within a module.

Within the block body (between `{` and `}`) are the configuration arguments
for the resource itself. Most arguments in this section depend on the
resource type, and indeed in this example both `ami` and `instance_type` are
arguments defined specifically for [the `aws_instance` resource type](/docs/providers/aws/r/instance.html).

-> **Note:** Resource names must start with a letter or underscore, and may
contain only letters, digits, underscores, and dashes.

## Resource Types

Each resource is associated with a single _resource type_, which determines
the kind of infrastructure object it manages and what arguments and other
attributes the resource supports.

### Providers

Each resource type is implemented by a [provider](./provider-requirements.html),
which is a plugin for Terraform that offers a collection of resource types. A
provider usually provides resources to manage a single cloud or on-premises
infrastructure platform. Providers are distributed separately from Terraform
itself, but Terraform can automatically install most providers when initializing
a working directory.

In order to manage resources, a Terraform module must specify which providers it
requires. Additionally, most providers need some configuration in order to
access their remote APIs, and the root module must provide that configuration.

For more information, see:

- [Provider Requirements](./provider-requirements.html), for declaring which
  providers a module uses.
- [Provider Configuration](./providers.html), for configuring provider settings.

Terraform usually automatically determines which provider to use based on a
resource type's name. (By convention, resource type names start with their
provider's preferred local name.) When using multiple configurations of a
provider (or non-preferred local provider names), you must use the `provider`
meta-argument to manually choose an alternate provider configuration. See
[the section on `provider` below][inpage-provider] for more details.

### Resource Arguments

Most of the arguments within the body of a `resource` block are specific to the
selected resource type. The resource type's documentation lists which arguments
are available and how their values should be formatted.

The values for resource arguments can make full use of
[expressions](./expressions.html) and other dynamic Terraform
language features.

There are also some _meta-arguments_ that are defined by Terraform itself
and apply across all resource types. (See [Meta-Arguments](#meta-arguments) below.)

### Documentation for Resource Types

Every Terraform provider has its own documentation, describing its resource
types and their arguments.

Most publicly available providers are distributed on the
[Terraform Registry](https://registry.terraform.io/browse/providers), which also
hosts their documentation. When viewing a provider's page on the Terraform
Registry, you can click the "Documentation" link in the header to browse its
documentation. Provider documentation on the registry is versioned, and you can
use the dropdown version menu in the header to switch which version's
documentation you are viewing.

To browse the publicly available providers and their documentation, see
[the providers section of the Terraform Registry](https://registry.terraform.io/browse/providers).

-> **Note:** Provider documentation used to be hosted directly on terraform.io,
as part of Terraform's core documentation. Although some provider documentation
might still be hosted here, the Terraform Registry is now the main home for all
public provider docs. (The exception is the built-in
[`terraform` provider](/docs/providers/terraform/index.html) for reading state
data, since it is not available on the Terraform Registry.)

## Resource Behavior

A `resource` block declares that you want a particular infrastructure object
to exist with the given settings. If you are writing a new configuration for
the first time, the resources it defines will exist _only_ in the configuration,
and will not yet represent real infrastructure objects in the target platform.

_Applying_ a Terraform configuration is the process of creating, updating,
and destroying real infrastructure objects in order to make their settings
match the configuration.

When Terraform creates a new infrastructure object represented by a `resource`
block, the identifier for that real object is saved in Terraform's
[state](/docs/state/index.html), allowing it to be updated and destroyed
in response to future changes. For resource blocks that already have an
associated infrastructure object in the state, Terraform compares the
actual configuration of the object with the arguments given in the
configuration and, if necessary, updates the object to match the configuration.

In summary, applying a Terraform configuration will:

- _Create_ resources that exist in the configuration but are not associated with a real infrastructure object in the state.
- _Destroy_ resources that exist in the state but no longer exist in the configuration.
- _Update in-place_ resources whose arguments have changed.
- _Destroy and re-create_ resources whose arguments have changed but which cannot be updated in-place due to remote API limitations.

This general behavior applies for all resources, regardless of type. The
details of what it means to create, update, or destroy a resource are different
for each resource type, but this standard set of verbs is common across them
all.

The meta-arguments within `resource` blocks, documented in the
sections below, allow some details of this standard resource behavior to be
customized on a per-resource basis.

### Accessing Resource Attributes

[Expressions](./expressions.html) within a Terraform module can access
information about resources in the same module, and you can use that information
to help configure other resources. Use the `<RESOURCE TYPE>.<NAME>.<ATTRIBUTE>`
syntax to reference a resource attribute in an expression.

In addition to arguments specified in the configuration, resources often provide
read-only attributes with information obtained from the remote API; this often
includes things that can't be known until the resource is created, like the
resource's unique random ID.

Many providers also include [data sources](./data-sources.html), which are a
special type of resource used only for looking up information.

For a list of the attributes a resource or data source type provides, consult
its documentation; these are generally included in a second list below its list
of configurable arguments.

For more information about referencing resource attributes in expressions, see
[Expressions: References to Resource Attributes](./expressions.html#references-to-resource-attributes).

### Resource Dependencies

Most resources in a configuration don't have any particular relationship, and
Terraform can make changes to several unrelated resources in parallel.

However, some resources must be processed after other specific resources;
sometimes this is because of how the resource works, and sometimes the
resource's configuration just requires information generated by another
resource.

Most resource dependencies are handled automatically. Terraform analyses any
[expressions](./expressions.html) within a `resource` block to find references
to other objects, and treats those references as implicit ordering requirements
when creating, updating, or destroying resources. Since most resources with
behavioral dependencies on other resources also refer to those resources' data,
it's usually not necessary to manually specify dependencies between resources.

However, some dependencies cannot be recognized implicitly in configuration. For
example, if Terraform must manage access control policies _and_ take actions
that require those policies to be present, there is a hidden dependency between
the access policy and a resource whose creation depends on it. In these rare
cases, [the `depends_on` meta-argument][inpage-depend] can explicitly specify a
dependency.

## Meta-Arguments

Terraform CLI defines the following meta-arguments, which can be used with
any resource type to change the behavior of resources:

- [`depends_on`, for specifying hidden dependencies][inpage-depend]
- [`count`, for creating multiple resource instances according to a count][inpage-count]
- [`for_each`, to create multiple instances according to a map, or set of strings][inpage-for_each]
- [`provider`, for selecting a non-default provider configuration][inpage-provider]
- [`lifecycle`, for lifecycle customizations][inpage-lifecycle]
- [`provisioner` and `connection`, for taking extra actions after resource creation][inpage-provisioner]

These arguments often have additional restrictions on what language features can
be used with them, which are described in each

### `depends_on`: Explicit Resource Dependencies

[inpage-depend]: #depends_on-explicit-resource-dependencies

Use the `depends_on` meta-argument to handle hidden resource dependencies that
Terraform can't automatically infer.

Explicitly specifying a dependency is only necessary when a resource relies on
some other resource's behavior but _doesn't_ access any of that resource's data
in its arguments.

This argument is available in all `resource` blocks, regardless of resource
type. For example:

```hcl
resource "aws_iam_role" "example" {
  name = "example"

  # assume_role_policy is omitted for brevity in this example. See the
  # documentation for aws_iam_role for a complete example.
  assume_role_policy = "..."
}

resource "aws_iam_instance_profile" "example" {
  # Because this expression refers to the role, Terraform can infer
  # automatically that the role must be created first.
  role = aws_iam_role.example.name
}

resource "aws_iam_role_policy" "example" {
  name   = "example"
  role   = aws_iam_role.example.name
  policy = jsonencode({
    "Statement" = [{
      # This policy allows software running on the EC2 instance to
      # access the S3 API.
      "Action" = "s3:*",
      "Effect" = "Allow",
    }],
  })
}

resource "aws_instance" "example" {
  ami           = "ami-a1b2c3d4"
  instance_type = "t2.micro"

  # Terraform can infer from this that the instance profile must
  # be created before the EC2 instance.
  iam_instance_profile = aws_iam_instance_profile.example

  # However, if software running in this EC2 instance needs access
  # to the S3 API in order to boot properly, there is also a "hidden"
  # dependency on the aws_iam_role_policy that Terraform cannot
  # automatically infer, so it must be declared explicitly:
  depends_on = [
    aws_iam_role_policy.example,
  ]
}
```

The `depends_on` meta-argument, if present, must be a list of references
to other resources in the same module. Arbitrary expressions are not allowed
in the `depends_on` argument value, because its value must be known before
Terraform knows resource relationships and thus before it can safely
evaluate expressions.

The `depends_on` argument should be used only as a last resort. When using it,
always include a comment explaining why it is being used, to help future
maintainers understand the purpose of the additional dependency.



### `provider`: Selecting a Non-default Provider Configuration

[inpage-provider]: #provider-selecting-a-non-default-provider-configuration

The `provider` meta-argument specifies which provider configuration to use,
overriding Terraform's default behavior of selecting one based on the resource
type name. Its value should be an unquoted `<PROVIDER>.<ALIAS>` reference.

As described in [Provider Configuration](./providers.html), you can optionally
create multiple configurations for a single provider (usually to manage
resources in different regions of multi-region services). Each provider can have
one default configuration, and any number of alternate configurations that
include an extra name segment (or "alias").

By default, Terraform interprets the initial word in the resource type name
(separated by underscores) as the local name of a provider, and uses that
provider's default configuration. For example, the resource type
`google_compute_instance` is associated automatically with the default
configuration for the provider named `google`.

By using the `provider` meta-argument, you can select an alternate provider
configuration for a resource:

```hcl
# default configuration
provider "google" {
  region = "us-central1"
}

# alternate configuration, whose alias is "europe"
provider "google" {
  alias  = "europe"
  region = "europe-west1"
}

resource "google_compute_instance" "example" {
  # This "provider" meta-argument selects the google provider
  # configuration whose alias is "europe", rather than the
  # default configuration.
  provider = google.europe

  # ...
}
```

A resource always has an implicit dependency on its associated provider, to
ensure that the provider is fully configured before any resource actions
are taken.

The `provider` meta-argument expects
[a `<PROVIDER>.<ALIAS>` reference](./providers.html#referring-to-alternate-providers),
which does not need to be quoted. Arbitrary expressions are not permitted for
`provider` because it must be resolved while Terraform is constructing the
dependency graph, before it is safe to evaluate expressions.


### `provisioner` and `connection`: Resource Provisioners

[inpage-provisioner]: #provisioner-and-connection-resource-provisioners

> **Hands-on:** To learn about more declarative ways to handle provisioning actions, try the [Provision Infrastructure Deployed with Terraform](https://learn.hashicorp.com/collections/terraform/provision?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS) collection on HashiCorp Learn.

Some infrastructure objects require some special actions to be taken after they
are created before they can become fully functional. For example, compute
instances may require configuration to be uploaded or a configuration management
program to be run before they can begin their intended operation.

Create-time actions like these can be described using _resource provisioners_.
A provisioner is another type of plugin supported by Terraform, and each
provisioner takes a different kind of action in the context of a resource
being created.

Provisioning steps should be used sparingly, since they represent
non-declarative actions taken during the creation of a resource and so
Terraform is not able to model changes to them as it can for the declarative
portions of the Terraform language.

Provisioners can also be defined to run when a resource is _destroyed_, with
certain limitations.

The `provisioner` and `connection` block types within `resource` blocks are
meta-arguments available across all resource types. Provisioners and their
usage are described in more detail in
[the Provisioners section](/docs/provisioners/index.html).

## Local-only Resources

While most resource types correspond to an infrastructure object type that
is managed via a remote network API, there are certain specialized resource
types that operate only within Terraform itself, calculating some results and
saving those results in the state for future use.

For example, local-only resource types exist for
[generating private keys](/docs/providers/tls/r/private_key.html),
[issuing self-signed TLS certificates](/docs/providers/tls/r/self_signed_cert.html),
and even [generating random ids](/docs/providers/random/r/id.html).
While these resource types often have a more marginal purpose than those
managing "real" infrastructure objects, they can be useful as glue to help
connect together other resources.

The behavior of local-only resources is the same as all other resources, but
their result data exists only within the Terraform state. "Destroying" such
a resource means only to remove it from the state, discarding its data.

## Operation Timeouts

Some resource types provide a special `timeouts` nested block argument that
allows you to customize how long certain operations are allowed to take
before being considered to have failed.
For example, [`aws_db_instance`](/docs/providers/aws/r/db_instance.html)
allows configurable timeouts for `create`, `update` and `delete` operations.

Timeouts are handled entirely by the resource type implementation in the
provider, but resource types offering these features follow the convention
of defining a child block called `timeouts` that has a nested argument
named after each operation that has a configurable timeout value.
Each of these arguments takes a string representation of a duration, such
as `"60m"` for 60 minutes, `"10s"` for ten seconds, or `"2h"` for two hours.

```hcl
resource "aws_db_instance" "example" {
  # ...

  timeouts {
    create = "60m"
    delete = "2h"
  }
}
```

The set of configurable operations is chosen by each resource type. Most
resource types do not support the `timeouts` block at all. Consult the
documentation for each resource type to see which operations it offers
for configuration, if any.
