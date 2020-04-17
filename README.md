# Stopover Resource

Generates a YAML file containing the versions of all resources that at task `get`s before getting this resource. Uses [stopover](https://github.com/EngineerBetter/stopover) to generate the file.

## Source Configuration

- `uri`: _Required_. The location of the concourse.
- `username`: _Required_. The username to log into Concourse with.
- `password`: _Required_. The password to log into Concourse with.
- `team`: _Optional_. The team to log in to. If not specified `main` is used.

## Examples

Resource type definition:

```yaml
resource_types:
- name: stopover
  type: docker-image
  source:
    repository: engineerbetter/stopover-resource
```

Resource definition:

```yaml
- name: stopover
  type: stopover
  source:
    uri: https://ci.engineerbetter.com
    username: a-user
    password: c00lp@ss
    team: ci
```

## Behaviour

### `check`

Generates a random version

### `get`: create a versions file

```yaml
- name: promote-versions
  plan:
  - in_parallel:
    - get: cf-smoke-tests
      trigger: true
      passed: [smoke-test]
    - get: cf-deployment
      trigger: true
      passed: [smoke-test]
    - get: bosh-deployment
      trigger: true
      passed: [smoke-test]
    - get: pipeline-promotion
      trigger: true
      passed: [smoke-test]
    - get: pcf-ops-image
      trigger: true
      passed: [smoke-test]
  - get: stopover
  - put: versions-s3
    params:
      file: stopover/versions.yml
```

`versions.yml` in the above example will look like:

```yaml
resource_version_bosh-deployment:
  ref: 5dd6e6863d537bfed6760b08884ab938a6496c59
resource_version_cf-deployment:
  id: "25465294"
  tag: v12.42.0
  timestamp: 2020-04-13T22:21:20Z
resource_version_cf-smoke-tests:
  ref: 93e9fb5f0d2083c0835c5daef9472c4a08723d0e
resource_version_pcf-ops-image:
  digest: sha256:9ed31d515c6059f1a8147ddb81fc04528d5913ef52e9e4cebfd819432e310272
resource_version_pipeline-promotion:
  ref: 2d9d15f365f5acd5305e6a379b73b001d5d67d04
resource_version_stopover:
  random: "3804"
```

### `put`

Not implemented
