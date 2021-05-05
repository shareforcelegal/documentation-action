# documentation-action

A Github action that is able to build the documentation for Shareforce projects.

This repository is heavily based on
[laminas/documentation-theme](https://github.com/laminas/documentation-theme)
and we adjusted it to our own needs. Keep in mind that you are free to use any
of the code here but you cannot use the repository as-is. The entire process has
been made for our organization.

Only use this action if you want to publish documentation to the `gh-pages` branch.

## How does it work?

The action searches for a `mkdocs.yml` file in your repository. If no `mkdocs.yml`
file is available in the repository, the action aborts early with no errors. When
the file has been found, the action builds the documentation using mkdocs. The markdown
files should be located in `docs/book/` and will be build to `docs/html/`.

As an example:

```yaml
- name: Build Docs
  uses: shareforcelegal/documentation-action@master
  env:
    DOCS_DEPLOY_KEY: ${{ secrets.DOCS_DEPLOY_KEY }}
  with:
    emptyCommits: false
    siteUrl: https://docs.shareforcelegal.com
```

The above will build docs in the `docs/html` subdirectory, will NOT build for
empty commits, and uses an alternate base site URL.

## Behaviors

- By default, the action auto-determines the `siteUrl` based on the repository,
  matching against the repository organization.

- By default, it will build even when the commit is empty.

## Options

- emptyCommits: Specify whether to build documentation for empty commits.
  Defaults to `true`.

- username: Specify a git username under which to make the documentation commit.
  If none is specified, it derives it from the user who made the commit that
  triggered the action.

- useremail Specify a git user email under which to make the documentation commit.
  If none is specified, it uses `${username}@users.noreply.github.com`.

- siteUrl: Specify the scheme and authority of the site URL under which
  documentation will be available. By default, this will be
  https://docs.shareforcelegal.com or be derived from the `${GITHUB_REPOSITORY}`
  value.

## Environment

This action requires the environment variable `$DOCS_DEPLOY_KEY`, which
should be a deployment key.

Generate a deployment key as follows:

```bash
$ ssh-keygen -t rsa -b 4096 -C "${git config user.email}" -f gh-pages -N ""
# Creates the files:
# - gh-pages (private SSH key)
# - gh-pages.pub (public key)
```

Go to the **Settings** tab of your repository.

Under **Deploy Keys**, select the **Add Key** button, add the contents of your
`gh-pages.pub` file, and select the "Allow Write Access" checkbox.

Then go to **Secrets**, select the **Add** button, give the new secret the title
`DOCS_DEPLOY_KEY`, and paste in the contents of your `gh-pages` file.

## Example workflow

```yaml
name: docs-build

on:
  push:
    branches:
      - master

jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Build and deploy documentation
        uses: shareforcelegal/documentation-action@master
        env:
          DOCS_DEPLOY_KEY: ${{ secrets.DOCS_DEPLOY_KEY }}
        with:
          emptyCommits: false
```
