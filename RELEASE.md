# Releasing RetroLab

## Automated releases

Releases are automated using GitHub Actions. They are triggered when a new tag is pushed to the remote.

To cut a new release, run the following:

```bash
# checkout the main branch
git checkout main

# for a patch release
jlpm release:patch

# for a minor version bump
jlpm release:bump minor

# for a major version bump
jlpm release:bump major

# push to the main branch
git push origin main --tags
```

We follow a similar bump strategy as in JupyterLab: https://github.com/jupyterlab/jupyterlab/blob/master/RELEASE.md#bump-version

The release workflow also creates a GitHub release with the new changes generated with [github-changelog-generator](https://github.com/github-changelog-generator/github-changelog-generator).

If you would still like to do the release manually instead, read below.

## Making a nanual new release of RetroLab

This process is still a bit manual and consists in running a couple of commands.

## Getting a clean environment

Creating a new environment can help avoid pushing local changes and any extra tag.

```bash
mamba create -q -y -n retrolab-release -c conda-forge twine nodejs jupyter-packaging jupyterlab -y
conda activate retrolab-release
```

Alternatively, the local repository can be cleaned with:

```bash
git clean -fdx
```

## Releasing on PyPI

Make sure the `dist/` folder is empty.

1. Update [retrolab/\_version.py](./retrolab/_version.py) with the new version number
2. Commit the changes

- `git add retrolab/_version.py`
- `git commit -m "Release x.y.z"`

3. Bump the frontend packages:

- `jlpm`
- `jlpm run lerna version x.y.z --no-push --amend --force-publish`

4. Run: `python setup.py sdist bdist_wheel`
5. Double check the size of the bundles in the `dist/` folder
6. Test the release by installing the wheel or sdist: `python -m pip install ./dist/retrolab-x.y.z-py3-none-any.whl
7. `export TWINE_USERNAME=mypypi_username`
8. `twine upload dist/*`

## Releasing on conda-forge

The simplest is to wait for the bot to automatically open the PR.

Alternatively, to do the update manually:

1. Open a new PR on https://github.com/conda-forge/retrolab-feedstock to update the `version` and the `sha256` hash
2. Wait for the tests
3. Merge the PR

The new version will be available on `conda-forge` soon after.

## Publish the packages to npm

1. Publish the packages: `jlpm run lerna publish from-package`

## Committing and tagging

Push the release commit to the `main` branch:

```bash
git push origin main
```

Then create a new release from the GitHub interface.
