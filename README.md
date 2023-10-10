# git-changelog

[![ci](https://github.com/pawamoy/git-changelog/workflows/ci/badge.svg)](https://github.com/pawamoy/git-changelog/actions?query=workflow%3Aci)
[![documentation](https://img.shields.io/badge/docs-mkdocs%20material-blue.svg?style=flat)](https://pawamoy.github.io/git-changelog/)
[![pypi version](https://img.shields.io/pypi/v/git-changelog.svg)](https://pypi.org/project/git-changelog/)
[![gitpod](https://img.shields.io/badge/gitpod-workspace-blue.svg?style=flat)](https://gitpod.io/#https://github.com/pawamoy/git-changelog)
[![gitter](https://badges.gitter.im/join%20chat.svg)](https://gitter.im/git-changelog/community)

Automatic Changelog generator using Jinja2 templates. From git logs to change logs.

## Differences of the Fork
- Based on **[git-changelog](https://github.com/pawamoy/git-changelog):[2.3.0](https://github.com/pawamoy/git-changelog/releases/tag/2.3.0)**

1. **Changed default behavior of the bump option and a new `--major-version-zero` parameter**
    - *Premise*: When your current version is *0.x.x* and the new commits contain a flag for global changes, using `git-changelog --bump=auto` will result in a minor version update in the changelog instead of a major version. To transition from *0.x.x* to *1.0.0*, one has to manually use `git-changelog --bump 1.0.0`. In `git-changelog`, there's no option to adjust the behavior of auto bumping versions for *0.x.x* releases.
    - *Solution in the fork*: By default, the major version is always updated in the presence of global changes, even if your release is *0.x.x*. A `--major-version-zero` parameter was added, following the example of the [commitizen](https://github.com/commitizen-tools/commitizen) tool, allowing to return to the original behavior.

2. **Customization of agreement types**
    - *Premise*: It's impossible to add your own types for parsing commits and to override the existing ones (e.g., for translating sections in the changelog).
    - *Solution in the fork*: Added the ability to configure your array of types through configuration files. When using customized types, there's the option to override which of the presented types will be a flag for updating the minor version.
3. If you use an origin in the form `https://user:token@host.com`, your `user:token` data won't end up in the URL.
4. Documentation and tests have been updated according to the changes made. More detailed usage examples can be seen in [docs/usage.md](https://github.com/sandzhaj/git-changelog/blob/main/docs/usage.md).

## Features

- [Jinja2][jinja2] templates!
  You get full control over the rendering.
  Built-in [Keep a Changelog][keep-a-changelog] and [Angular][angular] templates
  (also see [Conventional Changelog][conventional-changelog]).
- Commit styles/conventions parsing.
  Built-in [Angular][angular-convention], [Conventional Commit][conventional-commit] and basic conventions.
- Git service/provider agnostic,
  plus references parsing (issues, commits, etc.).
  Built-in [GitHub][github-refs], [Gitlab][gitlab-refs] and [Bitbucket][bitbucket-refs] support.
- Understands [Semantic Versioning][semantic-versioning]:
  major/minor/patch for versions and commits.
  Guesses next version based on last commits.
- Parses [Git trailers][git-trailers], allowing to reference
  issues, PRs, etc., in your commit messages
  in a clean, provider-agnostic way.

- Todo:
    - [Plugin architecture][issue-19],
      to support more commit conventions and git services.
    - [Template context injection][issue-17],
      to furthermore customize how your changelog will be rendered.
    - [Easy access to "Breaking Changes"][issue-14] in the templates.
    - [Commits/dates/versions range limitation ability][issue-16].

[jinja2]:                 http://jinja.pocoo.org/
[keep-a-changelog]:       http://keepachangelog.com/en/1.0.0/
[angular]:                https://github.com/angular/angular/blob/master/CHANGELOG.md
[conventional-changelog]: https://github.com/conventional-changelog/conventional-changelog
[semantic-versioning]:    http://semver.org/spec/v2.0.0.html
[angular-convention]:     https://github.com/angular/angular/blob/master/CONTRIBUTING.md#commit
[conventional-commit]:    https://www.conventionalcommits.org/en/v1.0.0/
[github-refs]:            https://help.github.com/articles/autolinked-references-and-urls/
[gitlab-refs]:            https://docs.gitlab.com/ce/user/markdown.html#special-gitlab-references
[bitbucket-refs]:         https://support.atlassian.com/bitbucket-cloud/docs/markup-comments
[git-trailers]:           https://git-scm.com/docs/git-interpret-trailers

[issue-14]: https://github.com/pawamoy/git-changelog/issues/14
[issue-15]: https://github.com/pawamoy/git-changelog/issues/15
[issue-16]: https://github.com/pawamoy/git-changelog/issues/16
[issue-17]: https://github.com/pawamoy/git-changelog/issues/17
[issue-19]: https://github.com/pawamoy/git-changelog/issues/19

## Installation

With `pip`:
```bash
pip install git-changelog
```

With [`pipx`](https://github.com/pipxproject/pipx):
```bash
python3.8 -m pip install --user pipx
pipx install git-changelog
```

## Usage (command-line)

```
usage: git-changelog [-b] [-h] [-i] [-g VERSION_REGEX] [-m MARKER_LINE]
                     [-o OUTPUT] [-r] [-R] [-I INPUT]
                     [-c {angular,conventional,basic}] [-s SECTIONS]
                     [-t {angular,keepachangelog}] [-T] [-E] [-v]
                     [REPOSITORY]

Automatic Changelog generator using Jinja2 templates.

This tool parses your commit messages to extract useful data
that is then rendered using Jinja2 templates, for example to
a changelog file formatted in Markdown.

Each Git tag will be treated as a version of your project.
Each version contains a set of commits, and will be an entry
in your changelog. Commits in each version will be grouped
by sections, depending on the commit convention you follow.

BASIC CONVENTION

Default sections:
- add: Added
- fix: Fixed
- change: Changed
- remove: Removed

Additional sections:
- merge: Merged
- doc: Documented

ANGULAR CONVENTION

Default sections:
- feat: Features
- fix: Bug Fixes
- revert: Reverts
- ref, refactor: Code Refactoring
- perf: Performance Improvements

Additional sections:
- build: Build
- chore: Chore
- ci: Continuous Integration
- deps: Dependencies
- doc, docs: Docs
- style: Style
- test, tests: Tests

CONVENTIONALCOMMIT CONVENTION

Default sections:
- feat: Features
- fix: Bug Fixes
- revert: Reverts
- ref, refactor: Code Refactoring
- perf: Performance Improvements

Additional sections:
- build: Build
- chore: Chore
- ci: Continuous Integration
- deps: Dependencies
- doc, docs: Docs
- style: Style
- test, tests: Tests

positional arguments:
  REPOSITORY            The repository path, relative or absolute. Default: .

options:
  -b, --bump-latest     Deprecated, use --bump=auto instead.
                        Guess the new latest version by bumping the previous
                        one based on the set of unreleased commits. For
                        example, if a commit contains breaking changes, bump
                        the major number (or the minor number for 0.x
                        versions). Else if there are new features, bump the
                        minor number. Else just bump the patch number.
                        Default: False.
  --bump VERSION        Specify the bump from latest version for the set
                        of unreleased commits. Can be one of 'auto',
                        'major', 'minor', 'patch' or a valid semver version
                        (eg. 1.2.3). With 'auto', if a commit contains breaking
                        changes, bump the major number, else if there are new features,
                        bump the minor number, else just bump the patch number.
                        Default: None.
  --major-version-zero  When true, breaking changes on a 0.x will remain as a 0.x version.
                        On false, a breaking change will bump a 0.x version to 1.0. 
                        major-version-zero. Default: unset (false).
  -h, --help            Show this help message and exit.
  -i, --in-place        Insert new entries (versions missing from changelog)
                        in-place. An output file must be specified. With
                        custom templates, you can pass two additional
                        arguments: --version-regex and --marker-line. When
                        writing in-place, an 'in_place' variable will be
                        injected in the Jinja context, allowing to adapt the
                        generated contents (for example to skip changelog
                        headers or footers). Default: False.
  -g, --version-regex VERSION_REGEX
                        A regular expression to match versions in the existing
                        changelog (used to find the latest release) when
                        writing in-place. The regular expression must be a
                        Python regex with a 'version' named group. Default:
                        ^## \[(?P<version>v?[^\]]+).
  -m, --marker-line MARKER_LINE
                        A marker line at which to insert new entries (versions
                        missing from changelog). If two marker lines are
                        present in the changelog, the contents between those
                        two lines will be overwritten (useful to update an
                        'Unreleased' entry for example). Default:
                        <!-- insertion marker -->.
  -o, --output OUTPUT   Output to given file. Default: stdout.
  -p {github,gitlab}, --provider {github,gitlab}
                        Explicitly specify the repository provider.
  -r, --parse-refs      Parse provider-specific references in commit messages
                        (GitHub/GitLab/Bitbucket issues, PRs, etc.). Default: False.
  -R, --release-notes   Output release notes to stdout based on the last entry
                        in the changelog. Default: False.
  -I, --input INPUT     Read from given file when creating release notes.
                        Default: CHANGELOG.md.
  -c, --style, --commit-style, --convention {angular,conventional,basic}
                        The commit convention to match against. Default:
                        basic.
  -s, --sections SECTIONS
                        A comma-separated list of sections to render. See the
                        available sections for each supported convention in
                        the description. Default: None.
  -t {angular,keepachangelog}, --template {angular,keepachangelog}
                        The Jinja2 template to use. Prefix with "path:" to
                        specify the path to a directory containing a file
                        named "changelog.md". Default: keepachangelog.
  -T, --trailers, --git-trailers
                        Parse Git trailers in the commit message. See
                        https://git-scm.com/docs/git-interpret-trailers.
                        Default: False.
  -E, --omit-empty-versions
                        Omit empty versions in the output. Default: False.
  -v, --version         Show the current version of the program and exit.
```
