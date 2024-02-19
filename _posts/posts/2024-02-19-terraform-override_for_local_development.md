---
layout: post
title: terraform override keeps your code clean when testing locally
updated: 2024-02-19
category: posts
---

## Clean and Testable Terraform-Code

Keep terraform code readable and clutter-free is no easy task.
And often overlooked feature is the terraform override mechanism.
Suppose you have this code and you cannot inject a mock-provider for
the "foo_resource_definitions", but on the other hand you would like
to test your helm_resources (using minikube or [KIND](https://kind.sigs.k8s.io/)).


contents of file foo-resources.tf
```
resource "foo_resource_definition" "foo" {
  for_each = {
    for index, db in(var.databases) :
    db.name => db
  }

  id          = each.value.name
  name        = each.value.name
  ...
}
```

contents of file helm.tf
```
resource "helm_release" "database" {
  for_each = {
    for index, db in(var.databases) :
    db.name => db
  }

  chart            = "pxc-db"
  name             = "database"
  namespace        = "database-${each.key}"
  ...
}
```

It would be fine to disable the "foo_resource_definitions" part, 
this way you could test the rest more easily.

So, you could use a construct like this, but this would be polluting your code and making
it harder to read:


```
resource "foo_resource_definition" "foo" {
  for_each = var.fake_enabled == false ? {
    for index, db in(var.databases) :
    db.name => db
  } : {}
```

Or, and that would be an even better solution, just leave the for_each block
untouched and move this to a new file and override the critical part.

Just copy the file "foo-resources.tf" into "foo-resources_override.tf"
and change the for_each block only in that file. 


## Final solution looks like this:

contents of file foo-resources.tf
```
resource "foo_resource_definition" "foo" {
  for_each = {
    for index, db in(var.databases) :
    db.name => db
  }

  id          = each.value.name
  name        = each.value.name
  ...
}
```

contents of file foo-resources_override.tf
```
resource "foo_resource_definition" "foo" {
  for_each = var.fake_enabled == false ? {
    for index, db in(var.databases) :
    db.name => db
  } : {}

  id          = each.value.name
  name        = each.value.name
  ...
}
```


