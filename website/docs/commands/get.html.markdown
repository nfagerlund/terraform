---
layout: "docs-cli"
page_title: "Command: get"
sidebar_current: "docs-commands-get"
description: |-
  The `terraform get` command is used to download and update modules.
---

# Command: get

The `terraform get` command is used to download and update
[modules](/docs/modules/index.html) mentioned in the root module.

## Usage

Usage: `terraform get [options]`

The modules are downloaded into a `.terraform` subdirectory of the current
working directory. Don't commit this directory to your version control
repository.

The `get` command supports the following option:

* `-update` - If specified, modules that are already downloaded will be
   checked for updates and the updates will be downloaded if present.
