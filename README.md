# renovate-30418

Reproduce the issue of Renovate. https://github.com/renovatebot/renovate/issues/30418

Renovate configuration files [renovate.json](renovate.json), [foo.json](foo.json), and [config.js](config.js) should be migrated, meaning `renovate-config-validator --strict` should fail.

This repository reproduces the issue that `renovate-config-validator --strict` on config.js passes unexpectedly, while renovate-config-validator on renovate.json fails expectedly.

## How To Reproduce

Please push a commit to the main branch or create a pull request.
Then GitHub Actions Workflow is run and the issue would be reproduced.

- https://github.com/suzuki-shunsuke/renovate-30418/actions/workflows/test.yaml
- [workflow](.github/workflows/test.yaml)

There are three jobs.

- js: `RENOVATE_CONFIG_FILE=config.js renovate-config-validator --strict`
- js-arg: `renovate-config-validator --strict config.js`
- renovate-json: `renovate-config-validator --strict` (Validate renovate.json)
- foo-json: `RENOVATE_CONFIG_FILE=foo.json renovate-config-validator --strict`
- foo-json-arg: `renovate-config-validator --strict foo.json`

### Expected behaviour

All jobs should fail as these configuration files should be migrated.

### Actual behaviour

`renovate-json` failed expectedly but `foo-json` and `js` passed unexpectedly.

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
