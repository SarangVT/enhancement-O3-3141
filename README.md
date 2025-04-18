:wave: *New to our project? Be sure to review the [OpenMRS 3 Frontend Developer Documentation](https://openmrs.github.io/openmrs-esm-core/#/). You may find the [Map of the Project](https://openmrs.github.io/openmrs-esm-core/#/main/map) especially helpful.* :teacher: 

![Node.js CI](https://github.com/openmrs/openmrs-esm-patient-management/actions/workflows/ci.yml/badge.svg)

# OpenMRS Patient Management

This repository contains frontend modules for O3. These modules relate to registering and editing patients, searching for existing patients, creating and managing patient lists, managing patient queues in an outpatient setting and creating, editing and managing patient appointments. The modules within this repository include:

- [Active visits app](packages/esm-active-visits-app/)
- [Appointments app](packages/esm-appointments-app/)
- [Bed management](packages/esm-bed-management-app/)
- [Patient list management](packages/esm-patient-list-management-app)
- [Patient registration](packages/esm-patient-registration-app)
- [Patient search](packages/esm-patient-search-app)
- [Service queues](packages/esm-service-queues-app/README.md)
- [Ward](packages/esm-ward-app)

## Setup

Check out the developer documentation [here](http://o3-dev.docs.openmrs.org).

This monorepo uses [yarn](https://yarnpkg.com).

To install the dependencies, run:

```bash
yarn install
```

To set up environment variables for the project, follow these steps:

1. Create a copy of the .env.example file by running the following command:

    ```bash
    cp example.env .env
    ```

2. Open the newly created .env file in the root of the project.

3. Add the environment variables you need.

Note: These variables are currently only used for end-to-end tests.

To start a dev server for a specific module, run:

```bash
yarn start --sources 'packages/esm-<insert-package-name>-app'
```

This command uses the [openmrs](https://www.npmjs.com/package/openmrs) tooling to fire up a dev server running `esm-patient-management` as well as the specified module.

You could provide `yarn start` with as many `sources` arguments as you require. For example, to run the patient registration and patient search modules only, use:

```bash
yarn start --sources 'packages/esm-patient-search-app' --sources 'packages/esm-patient-registration-app'
```

To run an app locally with the same [configuration that is set in the reference application](https://github.com/openmrs/openmrs-distro-referenceapplication/blob/main/frontend/config-core_demo.json), use:

```bash
yarn start --config-url /openmrs/spa/config-core_demo.json --sources 'packages/esm-<insert-package-name>-app'
```

## Troubleshooting

If you notice that your local version of the application is not working or that there's a mismatch between what you see locally versus what's in the reference application, you likely have outdated versions of core libraries. To update core libraries, run the following commands:

```bash
# Upgrade core libraries
yarn up openmrs @openmrs/esm-framework

# Reset version specifiers to `next`. Don't commit actual version numbers.
git checkout package.json

# Run `yarn` to recreate the lockfile
yarn
```

## Contributing

Please read our [contributing](http://o3-dev.docs.openmrs.org/#/getting_started/contributing) guide.

## Running tests

To run tests for all packages, run:

```bash
yarn turbo run test
```

To run tests in `watch` mode, run:

```bash
yarn turbo run test:watch
```

To run tests for a specific package, pass the package name to the `--filter` flag. For example, to run tests for `esm-patient-conditions-app`, run:

```bash
yarn turbo test --filter=@openmrs/esm-patient-conditions-app
```

To run a specific test file, run:

```bash
yarn turbo run test -- basic-search
```

The above command will only run tests in the file or files that match the provided string.

You can also run the matching tests from above in watch mode by running:

```bash
yarn turbo run test:watch --basic-search
```

To generate a `coverage` report, run:

```bash
yarn turbo run coverage
```

By default, `turbo` will cache test runs. This means that re-running tests wihout changing any of the related files will return the cached logs from the last run. To bypass the cache, run tests with the `force` flag, as follows:

```bash
yarn turbo run test --force
```

### Unit tests

To run unit tests, use:

```sh
yarn test
```

### E2E tests

To run E2E tests, make sure the dev server is running by using:

```sh
yarn start --sources 'packages/esm-*-app/'
```

Then, in a separate terminal, run:

```sh
yarn test-e2e --headed
```

Please read [our E2E testing guide](https://o3-docs.openmrs.org/docs/frontend-modules/end-to-end-testing) for more information about E2E testing.

### Updating Playwright

To upgrade your Playwright version, update both the package.json file and the [e2e/support/bamboo/playwright.Dockerfile](e2e/support/bamboo/playwright.Dockerfile).

## Design patterns

For documentation about our design patterns, please visit our [design system](https://zeroheight.com/23a080e38/p/880723--introduction) documentation website.

## Deployment

The `main` branch of this repo is deployed in a [demo environment](https://openmrs-spa.org/openmrs/spa).

## Configuration

This module is designed to be driven by configuration files.

## Version and release

To increment the version, run the following command:

```sh
yarn release
```

You will need to pick the next version number. We use minor changes (e.g. `3.2.0` → `3.3.0`)
to indicate big new features and breaking changes, and patch changes (e.g. `3.2.0` → `3.2.1`)
otherwise.

Note that this command will not create a new tag, nor publish the packages.
After running it, make a PR or merge to `main` with the resulting changeset.

Once the version bump is merged, go to GitHub and
[draft a new release](https://github.com/openmrs/openmrs-esm-patient-management/releases/new). 
The tag should be prefixed with `v` (e.g., `v3.2.1`), while the release title
should be the version number (e.g., `3.2.1`). The creation of the GitHub release
will cause GitHub Actions to publish the packages, completing the release process.

> Don't run `npm publish` or `yarn publish`. Use the above process.

### Bumping the Common Lib version

Make sure to bump the Common Lib version used here each time you cut a release of Patient Chart. Because Common Lib is marked as a peer dependency and a Webpack module federation shared dependency in the [Appointments app](packages/esm-appointments-app/package.json), the copy of the Common Lib that the framework loads is the first one that gets loaded at runtime when frontend modules are registered. If this happens to be a different version than what the Patient Chart expects, you might get some unexpected behavior in the Patient Chart. You can bump the Common Lib version by running the following command:

```sh
yarn up @openmrs/esm-patient-common-lib
git checkout package.json
yarn
```

