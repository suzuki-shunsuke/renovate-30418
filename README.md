# renovate-30418

Reproduce the issue of Renovate. https://github.com/renovatebot/renovate/issues/30418

Renovate configuration files [renovate.json](renovate.json), [foo.json](foo.json), and [config.js](config.js) should be migrated, meaning `renovate-config-validator --strict` should fail.

This repository reproduces the issue that `renovate-config-validator --strict` on config.js passes unexpectedly, while renovate-config-validator on renovate.json fails expectedly.

## How To Reproduce

Please push a commit to the main branch or create a pull request.
Then GitHub Actions Workflow is run and the issue would be reproduced.

- https://github.com/suzuki-shunsuke/renovate-30418/actions/workflows/test.yaml
- [workflow](.github/workflows/test.yaml)

There are five jobs.

- js: `RENOVATE_CONFIG_FILE=config.js renovate-config-validator --strict`
- js-arg: `renovate-config-validator --strict config.js`
- renovate-json: `renovate-config-validator --strict` (Validate renovate.json)
- foo-json: `RENOVATE_CONFIG_FILE=foo.json renovate-config-validator --strict`
- foo-json-arg: `renovate-config-validator --strict foo.json`

### Expected behaviour

All jobs should fail as these configuration files should be migrated.

### Actual behaviour

https://github.com/suzuki-shunsuke/renovate-30418/actions/runs/12041333480

Seems like this issue occurs when a configuration file is passed via `RENOVATE_CONFIG_FILE`.
The file format (.js or .json) isn't related.
And this issue doesn't occur when a configuration file is passed via a positional argument.

- js: :o:
- js-arg: :x:
- renovate-json: :x:
- foo-json: :o:
- foo-json-arg: :x:

```
Run node -v
v20.18.1

Run npm -v
10.8.2

Run renovate --version
39.31.3
```

renovate-json:

```
Run renovate-config-validator --strict
 INFO: Validating renovate.json
 WARN: Config migration necessary
       "oldConfig": {"extends": ["config:base"]},
       "newConfig": {"extends": ["config:recommended"]}
 WARN: config.js needs migrating
       "originalConfig": {"packageRules": [{"packagePatterns": ["foo"], "groupName": "foo"}]},
       "migratedConfig": {"packageRules": [{"groupName": "foo", "matchPackageNames": ["/foo/"]}]}
 INFO: Validating config.js
Error: Process completed with exit code 1.
```

js:

```
Run RENOVATE_CONFIG_FILE=config.js renovate-config-validator --strict
 WARN: config.js needs migrating
       "originalConfig": {"packageRules": [{"packagePatterns": ["foo"], "groupName": "foo"}]},
       "migratedConfig": {"packageRules": [{"groupName": "foo", "matchPackageNames": ["/foo/"]}]}
 INFO: Validating config.js
 INFO: Config validated successfully
```

foo-json:

```
Run RENOVATE_CONFIG_FILE=foo.json renovate-config-validator --strict
 WARN: foo.json needs migrating
       "originalConfig": {"extends": ["config:base"]},
       "migratedConfig": {"extends": ["config:recommended"]}
 INFO: Validating foo.json
 INFO: Config validated successfully
```

foo-json-arg:

```
Run renovate-config-validator --strict foo.json
 INFO: Validating foo.json
 WARN: Config migration necessary
       "oldConfig": {"extends": ["config:base"]},
       "newConfig": {"extends": ["config:recommended"]}
Error: Process completed with exit code 1.
```
