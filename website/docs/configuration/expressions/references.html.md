---
layout: "language"
page_title: "References to Values - Configuration Language"
---

# References to Named Values

Terraform makes several kinds of named values available. Each of these names is
an expression that references the associated value; you can use them as
standalone expressions, or combine them with other expressions to compute new
values.

## Types of Named Values

The main kinds of named values available in Terraform are:

- Resources
- Input variables
- Local values
- Child module outputs
- Data sources
- Filesystem and workspace info
- Block-local values

The sections below explain each kind of named value in detail.

Although many of these names use dot-separated paths that resemble
[attribute notation](./types.html#indices-and-attributes) for elements of object values, they are not
implemented as real objects. This means you must use them exactly as written:
you cannot use square-bracket notation to replace the dot-separated paths, and
you cannot iterate over the "parent object" of a named entity; for example, you
cannot use `aws_instance` in a `for` expression to iterate over every AWS
instance resource.

### Resources

`<RESOURCE TYPE>.<NAME>` represents a [managed resource](/docs/configuration/resources.html) of
the given type and name.

The value of a resource reference can vary, depending on whether the resource
uses `count` or `for_each`:

- If the resource doesn't use `count` or `for_each`, the reference's value is an
  object. The resource's attributes are elements of the object, and you can
  access them using [dot or square bracket notation](./types.html#indices-and-attributes).
- If the resource has the `count` argument set, the reference's value is a
  _list_ of objects representing its instances.
- If the resource has the `for_each` argument set, the reference's value is a
  _map_ of objects representing its instances.

Any named value that does not match another pattern listed below
will be interpreted by Terraform as a reference to a managed resource.

For more information about how to use resource references, see
[references to resource attributes](#references-to-resource-attributes) below.

### Input Variables

`var.<NAME>` is the value of the [input variable](/docs/configuration/variables.html) of the given name.

### Local Values

`local.<NAME>` is the value of the [local value](/docs/configuration/locals.html) of the given name.

### Child Module Outputs

* `module.<MODULE NAME>.<OUTPUT NAME>` is the value of the specified
  [output value](/docs/configuration/outputs.html) from a
  [child module](/docs/configuration/modules.html) called by the current module.

### Data Sources

* `data.<DATA TYPE>.<NAME>` is an object representing a
  [data resource](/docs/configuration/data-sources.html) of the given data
  source type and name. If the resource has the `count` argument set, the value
  is a list of objects representing its instances. If the resource has the `for_each`
  argument set, the value is a map of objects representing its instances.

### Filesystem and Workspace Info

* `path.module` is the filesystem path of the module where the expression
  is placed.
* `path.root` is the filesystem path of the root module of the configuration.
* `path.cwd` is the filesystem path of the current working directory. In
  normal use of Terraform this is the same as `path.root`, but some advanced
  uses of Terraform run it from a directory other than the root module
  directory, causing these paths to be different.
* `terraform.workspace` is the name of the currently selected
  [workspace](/docs/state/workspaces.html).

### Block-Local Values

Within the bodies of certain blocks, or in some other specific contexts,
there are other named values available beyond the global values listed above.
These local names are described in the documentation for the specific contexts
where they appear. Some of most common local names are:

- `count.index`, in resources that use
  [the `count` meta-argument](/docs/configuration/resources.html#count-multiple-resource-instances-by-count).
- `each.key` / `each.value`, in resources that use
  [the `for_each` meta-argument](/docs/configuration/resources.html#for_each-multiple-resource-instances-defined-by-a-map-or-set-of-strings).
- `self`, in [provisioner](/docs/provisioners/index.html) and
  [connection](/docs/provisioners/connection.html) blocks.

-> **Note:** Local names are often referred to as _variables_ or
_temporary variables_ in their documentation. These are not [input
variables](/docs/configuration/variables.html); they are just arbitrary names
that temporarily represent a value.

## Named Values and Dependencies

Constructs like resources and module calls often use references to named values
in their block bodies, and Terraform analyzes these expressions to automatically
infer dependencies between objects. For example, an expression in a resource
argument that refers to another managed resource creates an implicit dependency
between the two resources.

## References to Resource Attributes

The most common reference type is a reference to an attribute of a resource
which has been declared either with a `resource` or `data` block. Because
the contents of such blocks can be quite complicated themselves, expressions
referring to these contents can also be complicated.

Consider the following example resource block:

```hcl
resource "aws_instance" "example" {
  ami           = "ami-abc123"
  instance_type = "t2.micro"

  ebs_block_device {
    device_name = "sda2"
    volume_size = 16
  }
  ebs_block_device {
    device_name = "sda3"
    volume_size = 20
  }
}
```

The documentation for [`aws_instance`](/docs/providers/aws/r/instance.html)
lists all of the arguments and nested blocks supported for this resource type,
and also lists a number of attributes that are _exported_ by this resource
type. All of these different resource type schema constructs are available
for use in references, as follows:

* The `ami` argument set in the configuration can be used elsewhere with
  the reference expression `aws_instance.example.ami`.
* The `id` attribute exported by this resource type can be read using the
  same syntax, giving `aws_instance.example.id`.
* The arguments of the `ebs_block_device` nested blocks can be accessed using
  a [splat expression](./splat.html). For example, to obtain a list of
  all of the `device_name` values, use
  `aws_instance.example.ebs_block_device[*].device_name`.
* The nested blocks in this particular resource type do not have any exported
  attributes, but if `ebs_block_device` were to have a documented `id`
  attribute then a list of them could be accessed similarly as
  `aws_instance.example.ebs_block_device[*].id`.
* Sometimes nested blocks are defined as taking a logical key to identify each
  block, which serves a similar purpose as the resource's own name by providing
  a convenient way to refer to that single block in expressions. If `aws_instance`
  had a hypothetical nested block type `device` that accepted such a key, it
  would look like this in configuration:

    ```hcl
      device "foo" {
        size = 2
      }
      device "bar" {
        size = 4
      }
    ```

    Arguments inside blocks with _keys_ can be accessed using index syntax, such
    as `aws_instance.example.device["foo"].size`.

    To obtain a map of values of a particular argument for _labelled_ nested
    block types, use a [`for` expression](./for.html):
    `{for k, device in aws_instance.example.device : k => device.size}`.

When a resource has the
[`count`](/docs/configuration/resources.html#count-multiple-resource-instances-by-count)
argument set, the resource itself becomes a _list_ of instance objects rather than
a single object. In that case, access the attributes of the instances using
either [splat expressions](./splat.html) or index syntax:

* `aws_instance.example[*].id` returns a list of all of the ids of each of the
  instances.
* `aws_instance.example[0].id` returns just the id of the first instance.

When a resource has the
[`for_each`](/docs/configuration/resources.html#for_each-multiple-resource-instances-defined-by-a-map-or-set-of-strings)
argument set, the resource itself becomes a _map_ of instance objects rather than
a single object, and attributes of instances must be specified by key, or can
be accessed using a [`for` expression](./for.html).

* `aws_instance.example["a"].id` returns the id of the "a"-keyed resource.
* `[for value in aws_instance.example: value.id]` returns a list of all of the ids
  of each of the instances.

Note that unlike `count`, splat expressions are _not_ directly applicable to resources managed with `for_each`, as splat expressions must act on a list value. However, you can use the `values()` function to extract the instances as a list and use that list value in a splat expression:

* `values(aws_instance.example)[*].id`

### Values Not Yet Known

When Terraform is planning a set of changes that will apply your configuration,
some resource attribute values cannot be populated immediately because their
values are decided dynamically by the remote system. For example, if a
particular remote object type is assigned a generated unique id on creation,
Terraform cannot predict the value of this id until the object has been created.

To allow expressions to still be evaluated during the plan phase, Terraform
uses special "unknown value" placeholders for these results. In most cases you
don't need to do anything special to deal with these, since the Terraform
language automatically handles unknown values during expressions, so that
for example adding a known value to an unknown value automatically produces
an unknown value as the result.

However, there are some situations where unknown values _do_ have a significant
effect:

* The `count` meta-argument for resources cannot be unknown, since it must
  be evaluated during the plan phase to determine how many instances are to
  be created.

* If unknown values are used in the configuration of a data resource, that
  data resource cannot be read during the plan phase and so it will be deferred
  until the apply phase. In this case, the results of the data resource will
  _also_ be unknown values.

* If an unknown value is assigned to an argument inside a `module` block,
  any references to the corresponding input variable within the child module
  will use that unknown value.

* If an unknown value is used in the `value` argument of an output value,
  any references to that output value in the parent module will use that
  unknown value.

* Terraform will attempt to validate that unknown values are of suitable
  types where possible, but incorrect use of such values may not be detected
  until the apply phase, causing the apply to fail.

Unknown values appear in the `terraform plan` output as `(not yet known)`.
