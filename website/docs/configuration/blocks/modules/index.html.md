---
layout: "language"
page_title: "Modules Overview - Configuration Language"
---

# Modules

> **Hands-on:** Try the [Reuse Configuration with Modules](https://learn.hashicorp.com/collections/terraform/modules?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS) collection on HashiCorp Learn.

A _module_ is a container for multiple resources that are used together.

Every Terraform configuration has at least one module, known as its
_root module_, which consists of the resources defined in the `.tf` files in
the main working directory.

A module can call other modules, which lets you include the child module's
resources into the configuration in a concise way. Modules
can also be called multiple times, either within the same configuration or
in separate configurations, allowing resource configurations to be packaged
and re-used.

This section documents how to call a module from another configuration:

- [Module Blocks](/docs/configuration/modules.html) documents the syntax for
  calling a child module from a parent module, including meta-arguments like
  `for_each`.

- [Module Sources](/docs/modules/sources.html) documents what kinds of paths,
  addresses, and URIs can be used in the `source` argument of a module block.

- The Meta-Arguments section documents special arguments that can be used with
  every module, including
  [`providers`](/docs/configuration/blocks/meta-arguments/module-providers.html),
  [`depends_on`](/docs/configuration/blocks/meta-arguments/depends_on.html),
  [`count`](/docs/configuration/blocks/meta-arguments/count.html),
  and [`for_each`](/docs/configuration/blocks/meta-arguments/for_each.html).

For information about developing reusable modules, see
[Module Development](/docs/modules/index.html).
