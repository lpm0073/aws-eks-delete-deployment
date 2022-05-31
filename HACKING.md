Developer notes
===============

This document is for people who maintain and contribute to this
repository.

Commit messages
---------------

Commit messages follow the [Conventional
Commits](https://www.conventionalcommits.org/) format that is also
used by Tutor and Open edX. See
[OEP-0051](https://open-edx-proposals.readthedocs.io/en/latest/best-practices/oep-0051-bp-conventional-commits.html)
for details.

We enforce the prescribed [type
prefixes](https://open-edx-proposals.readthedocs.io/en/latest/best-practices/oep-0051-bp-conventional-commits.html#type)
to be used in commit messages via
[Gitlint](https://jorisroovers.com/gitlint/).

How to run tests
----------------

Add the following ```Repository Secrets``` to ```Settings -> Secrets -> Actions``` in your forked repository. Replace these sample values with your own:

```bash
    AWS_ACCESS_KEY_ID=YOUR_AWS_IAM_KEY
    AWS_SECRET_ACCESS_KEY=YOUR_AWS_IAM_SECRET
    AWS_ECR_REGISTRY=123456789042.dkr.ecr.us-east-2.amazonaws.com
    AWS_ECR_REPOSITORY=openedx
    AWS_REGION=us-east-1
```

Launch the Github Actions Workflow in your forked repository ```Actions -> Test this action```.

This repo uses [tox](https://tox.readthedocs.io/) for unit and
integration tests. It does not install `tox` for you, you should
follow [the installation
instructions](https://tox.readthedocs.io/en/latest/install.html) if
your local setup does not yet include `tox`.

You are encouraged to set up your checkout such
that the tests run on every commit, and on every push. To do so, run
the following command after checking out this repository:

```bash
git config core.hooksPath .githooks
```

Once your checkout is configured in this manner, every commit will run
a code style check (with [Flake8](https://flake8.pycqa.org/)), and
every push to a remote topic branch will result in a full `tox` run.

In addition, we use [GitHub
Actions](https://docs.github.com/en/actions) to run the same checks
on every push to GitHub.

*If you absolutely must,* you can use the `--no-verify` flag to `git
commit` and `git push` to bypass local checks, and rely on GitHub
Actions alone. But doing so is strongly discouraged.


How to cut a release
--------------------

This repository uses
[bumpversion](https://pypi.org/project/bumpversion/) for managing new
releases.

Before cutting a new release, open `CHANGELOG.md` and add a new
section like this:

```markdown
## Unreleased

* [Bug fix] Description of bug fix
* [Enhancement] Description of enhancement
```

Commit these changes on `main` as you normally would.

Then, use `tox -e bumpversion` to increase the version number:

-   `tox -e bumpversion patch`: creates a new point release (such as 3.6.1)
-   `tox -e bumpversion minor`: creates a new minor release, with the patch level set to 0 (such as 3.7.0)
-   `tox -e bumpversion major`: creates a new major release, with the minor and patch levels set to 0 (such as 4.0.0)

This creates a new commit, and also a new tag, named `v<num>`, where
`<num>` is the new version number.

Push these two commits (the one for the changelog, and the version
bump) to `origin`. Make sure you push the `v<num>` tag to `origin` as
well.

Then, build a new `sdist` package, and [upload it to
PyPI](https://packaging.python.org/tutorials/packaging-projects/#uploading-the-distribution-archives)
(with [twine](https://packaging.python.org/key_projects/#twine)):

```bash
rm dist/* -f
./setup.py sdist
twine upload dist/*
```
