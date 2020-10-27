---
layout: "language"
page_title: "Configuration Blocks"
---

# Configuration Blocks

Most of Terraform's features are controlled by top-level blocks in a
configuration file. This section documents all of Terraform's top-level (or
nearly-top-level) blocks.

- [Resources](/docs/configuration/resources.html) describe the real-world
  infrastructure objects you want Terraform to manage. They're the most important
  part of the Terraform language.

- [Providers](/docs/configuration/blocks/providers/index.html) are the plugins
  that implement resource types. Every configuration must declare which
  providers it requires, and most providers also require configuration (like
  endpoint URLs or cloud regions) before they can be used.

- [Input Variables](/docs/configuration/variables.html) serve as parameters for
  a Terraform module, so users can customize behavior without editing the source.

- [Output Values](/docs/configuration/outputs.html) are like return values for a
  Terraform module.

- [Local Values](/docs/configuration/locals.html) are a convenience feature for
  assigning a short name to an expression.

- [Module Calls](/docs/configuration/modules.html) allow a parent Terraform
  module (usually the root module) to include the contents of another module,
  making it easier to reuse infrastructure code.

- [Data Sources](/docs/configuration/data-sources.html) can fetch or compute
  information to make it available in expressions.

- [Backend Configuration](/docs/configuration/backend.html) configures where
  Terraform stores state and performs API operations.

- [Terraform Settings](/docs/configuration/terraform.html) configure various
  other aspects of Terraform's behavior.

