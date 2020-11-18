---
layout: "language"
page_title: "Variable Definitions Files - Files and Directories - Configuration Language"
---

# Variable Definitions Files

Terraform modules can declare [input variables](/docs/configuration/variables.html),
which allow their caller to customize their behavior. For the root module of a configuration,

Since root modules are called by performing a Terraform run, their variables are provided from

One way to set variable values for a configuration's root module is with
variable definitions files. These files have some resemblance to configuration
files, but are handled differently.

## File Extension

Variable definitions files use a file extension of  `.tfvars` or `.tfvars.json`.

## Special Filenames

There are two
