---
layout: "language"
page_title: "Providers - Configuration Language"
---

# Providers

Terraform relies on plugins called "providers" to interact with remote systems.

Terraform configurations must declare which providers they require, so that
Terraform can install and use them. Additionally, some providers require
configuration (like endpoint URLs or cloud regions) before they can be used.

- [Provider Requirements](/docs/configuration/provider-requirements.html)
  documents how to declare providers so Terraform can install them.

- [Provider Configuration](/docs/configuration/providers.html)
  documents how to configure settings for providers.

- [Dependency Lock File](/docs/configuration/dependency-lock.html)
  documents an additional HCL file that can be included with a configuration,
  which tells Terraform to always use a specific set of provider versions.

## About Providers

Providers are plugins. They are released on a separate rhythm from Terraform
itself, and each provider has its own series of version numbers.

Each provider plugin offers a set of
[resource types](/docs/configuration/resources.html#resource-types-and-arguments),
and defines for each resource type which arguments it accepts, which attributes
it exports, and how changes to resources of that type are actually applied to
remote APIs.

Most providers configure a specific infrastructure platform (either cloud or
self-hosted). Providers can also offer local utilities for tasks like
generating random numbers for unique resource names.

The [Terraform Registry](https://registry.terraform.io/browse/providers)
is the main directory of publicly available Terraform providers, and hosts
providers for most major infrastructure platforms. You can also write and
distribute your own Terraform providers, for public or private use.

> **Hands-on:** If you're interested in developing your own Terraform providers, try the [Call APIs with Terraform Providers](https://learn.hashicorp.com/collections/terraform/providers?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS) collection on HashiCorp Learn.

## Provider Installation

Terraform finds and installs providers when
[initializing a working directory](/docs/commands/init.html). It can
automatically download providers from a Terraform registry, or load them from a
local mirror or cache.

When you add a new provider to a configuration, Terraform must install the
provider in order to use it. If you are using a persistent working directory,
you can run `terraform init` again to install new providers.

Providers downloaded by `terraform init` are only installed for the current
working directory; other working directories can have their own installed
provider plugins. To help ensure that each working directory will use the same
selected versions, `terraform init` records its version selections in
your configuration's
[dependency lock file](/docs/configuration/dependency-lock.html), named
`.terraform.lock.hcl` and will always make those same selections unless
you run `terraform init -upgrade` to update them.

To save time and bandwidth, Terraform supports an optional plugin cache. You can
enable the cache using the `plugin_cache_dir` setting in
[the CLI configuration file](/docs/commands/cli-config.html).

For more information about provider installation, see
[the `terraform init` command](/docs/commands/init.html).
