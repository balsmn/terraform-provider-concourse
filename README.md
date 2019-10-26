# terraform-provider-concourse

## What

A terraform provider for concourse

## Why

`fly` is an amazing tool, but configuration using scripts running fly is not
ideal.

## Prerequisites

Install `go`, `glide`, and `terraform`.

## How to build and use

```
git clone https://github.com/alphagov/terraform-provider-concourse $GOPATH/src/github.com/alphagov/terraform-provider-concourse
cd $GOPATH/src/github.com/alphagov/terraform-provider-concourse
glide install
go build
cp terraform-provider-concourse ~/.terraform.d/plugins/darwin_amd64
```

# Example `terraform`

## Create a provider (using target from fly)

```
provider "concourse" {
  target = "target_name"
}
```

## Create a provider (using a local username and password)

Note: this is not basic authentication

```
provider "concourse" {
  url  = "https://wings.pivotal.io"
  team = "main"

  username = "localuser"
  password = "very-secure-password"
}
```

## Look up a team

```
data "concourse_team" "my_team" {
  team_name = "main"
}

output "my_team_name" {
  value = "${data.concourse_team.my_team.team_name}"
}

output "my_team_owners" {
  value = "${data.concourse_team.my_team.owners}"
}

output "my_team_members" {
  value = "${data.concourse_team.my_team.members}"
}

output "my_team_pipeline_operators" {
  value = "${data.concourse_team.my_team.pipeline_operators}"
}

output "my_team_viewers" {
  value = "${data.concourse_team.my_team.viewers}"
}
```

## Look up a pipeline

```
data "concourse_pipeline" "my_pipeline" {
  team_name     = "main"
  pipeline_name = "pipeline"
}

output "my_pipeline_team_name" {
  value = "${data.concourse_pipeline.my_pipeline.team_name}"
}

output "my_pipeline_pipeline_name" {
  value = "${data.concourse_pipeline.my_pipeline.pipeline_name}"
}

output "my_pipeline_is_exposed" {
  value = "${data.concourse_pipeline.my_pipeline.is_exposed}"
}

output "my_pipeline_is_paused" {
  value = "${data.concourse_pipeline.my_pipeline.is_paused}"
}

output "my_pipeline_json" {
  value = "${data.concourse_pipeline.my_pipeline.json}"
}

output "my_pipeline_yaml" {
  value = "${data.concourse_pipeline.my_pipeline.yaml}"
}
```
## Create a team

Supports `owners`, `members`, `pipeline_operators`, and `viewers`.

Specify users and groups by prefixing the strings:

* `user:`
* `group:`

```
resource "concourse_team" "my_team" {
  team_name = "my-team"

  owners = [
    "group:github:org-name",
    "group:github:org-name:team-name",
    "user:github:tlwr",
  ]

  viewers = [
    "user:github:samrees"
  ]
}
```

## Create a pipeline
```
resource "concourse_pipeline" "my_pipeline" {
  team_name     = "main"
  pipeline_name = "my-pipeline"

  is_exposed = true
  is_paused  = true

  pipeline_config        = "${file("pipeline-config.yml")}"
  pipeline_config_format = "yaml"
}

# OR

resource "concourse_pipeline" "my_pipeline" {
  team_name     = "main"
  pipeline_name = "my-pipeline"

  is_exposed = true
  is_paused  = true

  pipeline_config        = "${file("pipeline-config.json")}"
  pipeline_config_format = "json"
}
```
