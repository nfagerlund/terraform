---
layout: "language"
page_title: "Overview - Configuration Language"
---

# Terraform Language Documentation

This is the documentation for Terraform's configuration language. It is relevant
to users of Terraform CLI, Terraform Cloud, and Terraform Enterprise.

> **Hands-on:** Try the [Terraform: Get Started](https://learn.hashicorp.com/collections/terraform/aws-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS) collection on HashiCorp Learn.

_The Terraform language is Terraform's primary user interface._ In every edition
of Terraform, a configuration written in the Terraform language is always at the
heart of the workflow.

## What's in This Documentation?

This documentation focuses on the syntax and features of Terraform's
configuration language. It also includes information about how Terraform handles
state, since state is relevant to all editions of Terraform and is deeply tied
to the language's representation of resources.

**This is not the only documentation you will need in order to use Terraform.**
Depending on the task at hand, you might need to consult some of the following:

- [Terraform CLI Documentation](/docs/cli-index.html) — Terraform CLI is an
  open-source command-line application that manages infrastructure resources
  based on a configuration written in the Terraform language.
- [Terraform Cloud and Enterprise Documentation](/docs/cloud/index.html) — 
  Terraform Cloud (and its self-hosted form, Terraform Enterprise) is an
  application that helps teams use Terraform together. It implements many of the
  same Terraform workflows that Terraform CLI does, but handles some of them
  differently.
- [Provider References on the Terraform Registry](https://registry.terraform.io) —
  All of Terraform's resource types are implemented by provider plugins.
  Providers are distributed on the Terraform Registry, and each provider's
  registry page includes documentation for its resource types and settings.

## Where Should You Start?

- **New user?** Try the
  [Get Started collection](https://learn.hashicorp.com/collections/terraform/aws-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS)
  at HashiCorp Learn, then return
  here once you've used Terraform to manage some simple resources.
- **Curious about Terraform?** See [Introduction to Terraform](/intro/index.html)
  for a broad overview of what Terraform is and why people use it.
- **Diving in?** Read the [Language Overview](./overview.html) to get oriented;
  this page explains the structure and purpose of the language and provides some
  realistic example code. Then, browse the navigation sidebar to find detailed
  information about all of the language's features.
