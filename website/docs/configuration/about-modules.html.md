---
layout: "language"
page_title: "About Modules - Configuration Language"
---

# About Modules

> **Hands-on:** Try the [Reuse Configuration with Modules](https://learn.hashicorp.com/collections/terraform/modules?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS) collection on HashiCorp Learn.

Modules are a way to reuse and share Terraform code.

Building infrastructure with Terraform usually involves declaring groups of resources that work together. Modules let you encapsulate a handful of closely related resources, and manage them with a smaller, more expressive interface. For example, an AWS VPC module might declare a VPC along with all of the security groups, subnets, route tables, and gateways necessary to make it useful.

A Terraform configuration can include a module's resources using a [module call](/docs/configuration/modules.html) block, which resembles a resource. Module calls can set values for a module's input variables, and you can include the same module in a configuration multiple times to get multiple sets of slightly different resources.

## How to Find Modules

The [Terraform Registry](https://registry.terraform.io/browse/modules) hosts a broad collection of publicly available Terraform modules for configuring many kinds of common infrastructure. These modules are free to use, and Terraform can download them automatically if you specify the appropriate source and version in a module call block.

Additionally, other members of your organization might produce modules specifically crafted for your own infrastructure needs. [Terraform Cloud](/docs/cloud/index.html) and [Terraform Enterprise](/docs/enterprise/index.html) include a private module registry for sharing modules internally within your organization.

## How to Use Modules

You can include the contents of a remote or local module in a configuration by using a [module call](/docs/configuration/modules.html) block.

## How to Develop Modules

A module is just a set of Terraform configuration files in a single directory, so technically you could repurpose any Terraform configuration as a reusable module.

However, making convenient and practical modules involves a lot of choices. For more information about best practices and the extra considerations involved in making your modules reusable, see [Module Development](/docs/modules/index.html).
