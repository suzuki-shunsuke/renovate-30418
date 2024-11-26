# renovate-30418

Reproduce the issue of Renovate. https://github.com/renovatebot/renovate/issues/30418

Renovate configuration files [renovate.json](renovate.json) and [config.js](config.js) should be migrated, meaning `renovate-config-validator --strict` should fail.

This repository reproduces the issue that `renovate-config-validator --strict` on config.js passes unexpectedly, while renovate-config-validator on renovate.json fails expectedly.

## How To Reproduce

Please push a commit to the main branch or create a pull request.
Then GitHub Actions Workflow is run and the issue would be reproduced.

[workflow](.github/workflows/test.yaml)

There are three jobs.

- js: Validate `config.js`
- json: Validate `renovate.json`
- json-env: Pass `renovate.json` via the environment variable `RENOVATE_CONFIG_FILE` and validate `renovate.json`

### Expected behaviour

These jobs should fail as these configuration files should be migrated.

### Actual behaviour

`json` and `json-env` failed expectedly but `js` passed unexpectedly.
