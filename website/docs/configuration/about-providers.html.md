---
layout: "language"
page_title: "About Providers - Configuration Language"
---

# About Providers

The core purpose of the Terraform language is [declaring resources](./resources.html)
in order to create and manage infrastructure resources such as physical
machines, VMs, network switches, containers, accounts, and more. Almost any
infrastructure type can be represented as a resource in Terraform.

To manage these resources, Terraform relies on plugins called "providers."

## What Providers Do

Each provider adds a set of [resource types](./resources.html) and/or
[data sources](./data-sources.html) that Terraform can manage. On its own,
Terraform's core can manage nothing; with the right providers, it can manage
anything.

Most providers configure a specific infrastructure platform (either cloud or
self-hosted). Providers can also offer local utilities for tasks like
generating random numbers for unique resource names.

## Where Providers Come From

Providers are distributed separately from Terraform itself, and each provider
has its own release cadence and version numbers.

The [Terraform Registry](https://registry.terraform.io/browse/providers)
is the main directory of publicly available Terraform providers, and hosts
providers for most major infrastructure platforms. You can also write and
distribute your own Terraform providers, for public or private use.

Some providers are developed and published by HashiCorp, some are published by
the maintainers of the infrastructure platform that they configure, and some are
published by users and volunteers. You can also write and distribute your own
providers, for public or private use.

## How to Find Providers

To find providers for the infrastructure platforms you use, browse
[the providers section of the Terraform Registry](https://registry.terraform.io/browse/providers).

The Terraform Registry hosts providers from many different publishers. The
provider listings use the following badges to indicate who develops and
maintains a given provider.

<table border="0" style="border-collapse: collapse; width: 100%;">
<tbody>
<tr style="height: 21px;">
<td style="width: 12.4839%; height: 21px;"><strong>Tier</strong></td>
<td style="width: 55.7271%; height: 21px;"><strong>Description</strong></td>
<td style="width: 31.7889%; height: 21px;"><strong>Namespace</strong></td>
</tr>
<tr style="height: 21px;">
<td style="width: 12.4839%; height: 21px;"><img src="/docs/registry/providers/images/official-tier.png" alt="" /></td>
<td style="width: 55.7271%; height: 21px;"><i><span style="font-weight: 400;">Official providers are owned and maintained by HashiCorp </span></i></td>
<td style="width: 31.7889%; height: 21px;"><code><span style="font-weight: 400;">hashicorp</span></code></td>
</tr>
<tr style="height: 21px;">
<td style="width: 12.4839%; height: 21px;"><img src="/docs/registry/providers/images/verified-tier.png" alt="" /></td>
<td style="width: 55.7271%; height: 21px;"><i><span style="font-weight: 400;">Verified providers are owned and maintained by third-party technology partners. Providers in this tier indicate HashiCorp has verified the authenticity of the Provider&rsquo;s publisher, and that the partner is a member of the </span></i><a href="https://www.hashicorp.com/ecosystem/become-a-partner/"><i><span style="font-weight: 400;">HashiCorp Technology Partner Program</span></i></a><i><span style="font-weight: 400;">.</span></i></td>
<td style="width: 31.7889%; height: 21px;"><span style="font-weight: 400;">Third-party organization, e.g. </span><code><span style="font-weight: 400;">mongodb/mongodbatlas</span></code></td>
</tr>
<tr style="height: 21px;">
<td style="width: 12.4839%; height: 21px;"><img src="/docs/registry/providers/images/community-tier.png" alt="" /></td>
<td style="width: 55.7271%; height: 21px;">Community providers are published to the Terraform Registry by individual maintainers, groups of maintainers, or other members of the Terraform community.</td>
<td style="width: 31.7889%; height: 21px;"><br />Maintainer&rsquo;s individual or organization account, e.g. <code>DeviaVir/gsuite</code></td>
</tr>
<tr style="height: 21px;">
<td style="width: 12.4839%; height: 21px;"><img src="/docs/registry/providers/images/archived-tier.png" alt="" /></td>
<td style="width: 55.7271%; height: 21px;">Archived Providers are Official or Verified Providers that are no longer maintained by HashiCorp or the community. This may occur if an API is deprecated or interest was low.</td>
<td style="width: 31.7889%; height: 21px;"><code>hashicorp</code> or third-party</td>
</tr>
</tbody>
</table>


## How to Use Providers

To use resources from a given provider, you need to include some information
about that provider in your configuration.

- Use [provider requirements](/docs/configuration/provider-requirements.html) to
  tell Terraform which providers you need, where those providers come from, and
  what versions of those providers are acceptable.

    With this information, Terraform can automatically install most providers
    during initialization.
- Most providers also require some configuration (like credentials, endpoint
  URLs, or cloud regions) before they can access the APIs they rely on. Use
  [provider configuration blocks](/docs/configuration/providers.html) to provide
  these settings.

## Documentation for Providers

Each provider has its own documentation, which details which resources and data
sources are available, how to use those resources and data sources, and any
settings the provider itself requires.

The [Terraform Registry](https://registry.terraform.io) is the main home for
provider documentation. When viewing a provider's page on the Terraform
Registry, you can click the "Documentation" link in the header to browse its
documentation. Provider documentation in the registry is versioned, and you can
use the dropdown version menu in the header to switch which version's
documentation you are viewing.

The Terraform language documentation also includes some
[lists of common providers](/docs/providers/index.html), with links to their
documentation. For historical reasons, some of this provider documentation is
still hosted as part of the Terraform language docs.

## How to Develop Providers

A provider is responsible for defining the arguments each resource type (or data
source) accepts, the attributes it exports, and the actual remote API calls that
should occur for any create/read/update/delete operations that Terraform needs
to perform.

Providers are written in Go, using the Terraform Plugin SDK. For more
information on developing providers, see:

- The [Extending Terraform](/docs/extend/index.html) documentation
- The [Call APIs with Terraform Providers](https://learn.hashicorp.com/collections/terraform/providers?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS)
  collection on HashiCorp Learn

