---
layout: "docs"
page_title: "Import"
sidebar_current: "docs-import"
description: |-
  Terraform is able to import existing infrastructure. This allows you take
  resources you've created by some other means and bring it under Terraform
  management.
---

# Import

> **Hands-on:** Try the [Import Terraform Configuration](https://learn.hashicorp.com/tutorials/terraform/state-import?in=terraform/state&utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS) tutorial on HashiCorp Learn.

Terraform is able to import existing infrastructure. This allows you take
resources you've created by some other means and bring it under Terraform
management.

This is a great way to slowly transition infrastructure to Terraform, or
to be able to be confident that you can use Terraform in the future if it
potentially doesn't support every feature you need today.

~> Warning: Terraform expects that each remote object it is managing will be
bound to only one resource address, which is normally guaranteed by Terraform
itself having created all objects. If you import existing objects into Terraform,
be careful to import each remote object to only one Terraform resource address.
If you import the same object multiple times, Terraform may exhibit unwanted
behavior. For more information on this assumption, see
[the State section](/docs/state/).

## Configuration is Not Auto-Generated

The current implementation of Terraform import can only import resources
into the [state](/docs/state). It does not generate configuration for those
resources.

This means importing a resource is a two-step process:

1. Run `terraform import` to associate a real-world infrastructure object with a
   named resource in Terraform's state.
2. Write a [resource block](/docs/configuration/blocks/resources/index.html) for
   a resource of the same name and type.

Terraform treats an imported resource the same way it treats a resource created
in a previous Terraform run: it does nothing if the configured arguments match
the current real-world state, modifies it if the arguments and state don't
match, and destroys it if there is no corresponding configuration block. See
[resource behavior](/docs/configuration/blocks/resources/behavior.html)
for more information.

## Remote Backends

When using Terraform import on the command line with a [remote
backend](/docs/backends/types/remote.html), such as Terraform Cloud, the import
command runs locally, unlike commands such as apply, which run inside your
Terraform Cloud environment. Because of this, the import command will not have
access to information from the remote backend, such as workspace variables.

In order to use Terraform import with a remote state backend, you may need to
set local variables equivalent to the remote workspace variables. You may also
need to set environment variables in your shell for provider credentials.
