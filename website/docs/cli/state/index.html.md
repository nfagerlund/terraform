---
layout: "docs"
page_title: "Manipulating State - Terraform CLI"
---

# Manipulating Terraform State

Terraform relies on [state data](/docs/state/index.html) to remember which
real-world object corresponds to which managed resource in the configuration;
this allows it to modify an existing object when changes are made to the
corresponding resource declaration. For infrastructure managed by multiple
people, the state data must be made available to all collaborators using a
[backend](/docs/configuration/backend.html) that stores state remotely.

Under normal conditions, Terraform updates state automatically during plans and
applies. However, it's sometimes necessary to make deliberate adjustments to
Terraform's state data, usually to compensate for changes to the configuration
or the real managed infrastructure.

When such situations arise, you can use one of Terraform CLI's commands for
inspecting and modifying state data.

Most of these commands are designed to help with a specific type of situation.

~> **Important:** Modifying state data outside a normal plan or apply is
inherently dangerous, because it can cause Terraform to lose track of managed
resources. Depending on your provisioning practices and infrastructure usage,
losing track of resources might cost you a lot of money, annoy all of your
colleagues, or even compromise the security of your operations. Make sure to
keep backups of your state data when modifying state out-of-band.

## Checking Things Out

- Use [the `terraform state list` command](/docs/commands/state/list.html) and
  [the `terraform state show` command](/docs/commands/state/show.html) to
  inspect state data before using any of the other state commands to do anything
  risky. (Or just use them when you're curious about something.)
- Use [the `terraform refresh` command](/docs/commands/refresh.html) to update
  state data to match the real-world condition of the managed resources. This is
  done automatically during plans and applies, but not during any of the direct
  state operations listed below, so you might want to refresh before reading or
  modifying state if it's been a few hours since the last plan or apply.

## Importing Pre-existing Resources

The `terraform import` command can associate unmanaged resources with a resource
address in a configuration, bringing them under Terraform's control.

Importing is documented in its own section of the Terraform CLI docs. See
[Importing Infrastructure](/docs/import/index.html) for details.

## Forcing Re-creation of Resources (Tainting)

When a resource declaration is modified, Terraform usually attempts to update
the existing resource in place (although some changes can require destruction
and re-creation, usually due to upstream API limitations).

In some cases, you might want a resource to be destroyed and re-created even
when Terraform doesn't think it's necessary. This is usually for objects that
aren't fully described by their resource arguments due to side-effects that
happen during creation; for example, a virtual machine that configures itself
with `cloud-init` on startup might no longer meet your needs if the cloud-init
configuration changes.

[The `terraform taint` command](/docs/commands/taint.html) tells Terraform to
destroy and re-create a particular resource during the next apply, regardless of
whether its resource arguments would normally require that. Tainting works by
marking the state data, so it will take effect on the next apply even if that
apply is performed by someone other than yourself.

[The `terraform untaint` command](/docs/commands/untaint.html) undoes a previous
taint, and can also preserve a resource that was automatically tainted due to
failed [provisioners](/docs/provisioners/index.html).

## Moving Resources

Terraform's state associates each real-world object with a configured resource
at a specific [resource address](/docs/internals/resource-addressing.html). This
is seamless when changing a resource's attributes, but Terraform will lose track
of a resource if you change its name or move it to a different module.

Often this is fine; Terraform will destroy the old resource, but will recreate a
similar resource in its place, associate it with the new resource address, and
update any resources that rely on that resource's attributes accordingly.
However, sometimes you don't want a resource to be destroyed and recreated, due
to cost or a desire for continuity.

- [The `terraform state mv` command](/docs/commands/state/mv.html) can change
  which resource address in your configuration is associated with a particular
  real-world object. Use this to preserve an object when renaming a resource, or
  when moving a resource into or out of a child module.
- [The `terraform state rm` command](/docs/commands/state/rm.html) tells
  Terraform to stop managing a resource as part of the current working directory
  and workspace, _without_ destroying the corresponding real-world object. You
  might use this to remove a resource from management entirely, or you might
  want to bring it under management using a completely different Terraform
  configuration (in which case you would subsequently use `terraform import` in
  a different working directory to bring the resource into its new home).

    Note that if you use this and don't subsequently delete the resource from
    the configuration, Terraform will create a new resource to take its place
    during the next apply.

## Changing Providers

All of Terraform's resource types are implemented by
[provider plugins](/docs/configuration/provider-requirements.html). The most
popular providers are published by HashiCorp or by the maintainers of the
infrastructure platforms that they configure, but anyone can publish their own
providers.

In some cases, it might make sense to use a personal fork of a popular provider
if the main release doesn't meet a particular need of your business. If you
decide to fork a provider but already use the upstream release to manage
important resources, Terraform won't automatically transfer all of those
resources to your new fork; resources in state data are associated with the
canonical source address of the provider that implements them, so replacing a
provider can cause all existing resources to be destroyed and re-created.

If you need to replace a provider with a similar plugin that implements the same
resource types, you can use
[the `terraform state replace-provider` command](/docs/commands/state/replace-provider.html)
to transfer existing resources to the new provider without requiring them to be
re-created.

Note that this can only yield good results if the two providers are very closely
related and implement the affected resources in very nearly the same way.

## Recovering From Disasters

If something has gone horribly wrong (possibly due to accidents when performing
some of the other state manipulation actions listed above), you might need to
take drastic actions with your state data.

- [The `terraform force-unlock` command](/docs/commands/force-unlock.html) can
  override the protections Terraform uses to prevent two processes from
  modifying state at the same time. You might need this if a Terraform process
  (like a normal apply) is unexpectedly terminated (like by the complete
  destruction of the VM it's running in) before it can release its lock on the
  state backend. Do not run this until you are completely certain what happened
  with the process that caused the lock to get stuck.
- [The `terraform state pull` command](/docs/commands/state/pull.html) and
  [the `terraform state push` command](/docs/commands/state/push.html) can
  directly read and write entire state files from and to the configured backend.
  You might need this for obtaining or restoring a state backup.

