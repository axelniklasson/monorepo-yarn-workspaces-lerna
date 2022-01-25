# Monorepo with yarn workspaces and Turborepo
This repo is a boilerplate for how a JS monorepo can be setup using [yarn workspaces](https://classic.yarnpkg.com/lang/en/docs/workspaces/) and [Turborepo](https://turborepo.org/) along with GitHub actions for CI.

## Tooling
### generate-project
The bundled `generate-project` script can be used to scaffold apps/packages with minimal sample code. To modify the templates, head on over to `tools/generate-project/templates` and modify accordingly.

```
➜  yarn generate-project
yarn run v1.22.17
$ node ./tools/generate-project/generate.js
Hi, welcome to generate-project
? What do you want to generate? Package
? What's the name of the project? utils
Success! Please run `yarn` in the project root.
✨  Done in 2.34s.

➜ yarn
yarn install v1.22.17
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 🔨  Building fresh packages...
✨  Done in 3.60s.

➜  yarn workspace @monorepo/utils test
yarn workspace v1.22.17
yarn run v1.22.17
warning package.json: No license field
$ jest
 PASS  ./index.test.js
  utils
    ✓ it sums correctly (2 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.818 s
Ran all test suites.
✨  Done in 1.79s.
✨  Done in 2.02s.
```

## Turbo pipeline
The Turbo pipeline has been setup to run the following
```
lint -> test -> build
```
in sequenece, so for every successful build both linting and tests will also have been run.

## CI workflow
### Pushes
Whenever a push is made to a branch, the following happens;
- If the branch *does not* have a parent (i.e. the `main` branch), `yarn turbo run build` is run which triggers a build in every app/package in the monorepo
- If the branch *does* have a parent, `yarn turbo run build --since=origin/<parent_branch>` is run which triggers a build in every app/package that has been changed since branched out from the parent branch

### Pull requests
Whenever a pull request is opened, the following happens:
- `yarn turbo run build --since=origin/<base_branch>` is run which triggers a build in every app/package in the monorepo that would be changed if the PR would be merged into the base branch
