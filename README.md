# Conventional Changelog plugin for release-it

This plugin will provide the recommended bump to release-it, and update the changelog file (e.g. `CHANGELOG.md`).

```
npm install --save-dev @release-it/conventional-changelog
```

## Configuration

In the [release-it](https://github.com/release-it/release-it) config, for example:

```json
"plugins": {
  "@release-it/conventional-changelog": {
    "preset": "angular",
    "infile": "CHANGELOG.md"
  }
}
```

Options are passed verbatim to
[conventional-recommended-bump](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-recommended-bump#readme)
and
[conventional-changelog-core](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-core#api).

### `preset`

Use one of:

- `angular`
- `atom`
- `codemirror`
- `conventionalcommits`
- `ember`
- `eslint`
- `express`
- `jquery`
- `jscs`
- `jshint`

Use an object with `name` and `types` to use a custom preset:

```json
"plugins": {
  "@release-it/conventional-changelog": {
    "infile": "CHANGELOG.md",
    "preset": {
      "name": "conventionalcommits",
      "types": [
        {
          "type": "feat",
          "section": "Features"
        },
        {
          "type": "fix",
          "section": "Bug Fixes"
        },
        {}
      ]
    }
  }
}
```

See the
[Conventional Changelog Configuration Spec (v2.1.0)](https://github.com/conventional-changelog/conventional-changelog-config-spec/blob/master/versions/2.1.0/README.md)
for the configuration object to pass as `preset`.

### `infile`

Default value: `undefined`

- Set a filename as `infile` to write the changelog to. If this file does not exist yet, it's created with the full
  history.
- When `infile` is not set, the changelog generated by this plugin will still be used as release notes for e.g.
  [GitHub Releases](https://github.com/release-it/release-it/blob/master/docs/github-releases.md).
- Set `infile: false` to disable the changelog writing (and only use the recommended bump for the next version).

### `header`

Set the main header for the changelog document:

```json
{
  "plugins": {
    "@release-it/conventional-changelog": {
      "infile": "CHANGELOG.md",
      "header": "# Changelog",
      "preset": {
        "name": "conventionalcommits"
      }
    }
  }
}
```

### `ignoreRecommendedBump`

Default value: `false`

Use `true` to ignore the recommended bump, and use the version provided by release-it (command line argument or prompt).

(Note that the changelog preview shows the recommended bump, as the desired version isn't known yet. The `infile` will
have the correct version.)

### `strictSemVer`

Default value: `false`

Use `true` to strictly follow semver, also in consecutive pre-releases. This means that from a pre-release, a
recommended bump will result in a next pre-release for the next version. For example, from `1.0.0-alpha.0` a recommended
bump of `minor` will result in a `preminor` bump to `1.1.0-alpha.0` (whereas the default behavior without this flag
results in a `prerelease` bump to `1.0.0-alpha.1`).

### `context`

Default value: `undefined`

This option will be passed as the second argument (`context`) to
[conventional-changelog-core](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-core#context),
for example:

```json
"plugins": {
  "@release-it/conventional-changelog": {
    "context": {
      "linkCompare": false
    }
  }
}
```

### `gitRawCommitsOpts`

Default value: `undefined`

Options for
[`git-raw-commits`](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/git-raw-commits#api).
For example, you can use the following option to include merge commits into changelog:

```json
{
  "plugins": {
    "@release-it/conventional-changelog": {
      "gitRawCommitsOpts": {
        "merges": null
      }
    }
  }
}
```

### `parserOpts`

Default value: `undefined`

Options for
[`conventional-commits-parser`](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-commits-parser#api).
For example, you can use the following option to set the merge pattern during parsing the commit message:

```json
{
  "plugins": {
    "@release-it/conventional-changelog": {
      "parserOpts": {
        "mergePattern": "^Merge pull request #(\\d+) from (.*)$"
      }
    }
  }
}
```

### `writerOpts`

Default value: `undefined`

Options for
[`conventional-changelog-writer`](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-writer#api).
For example, you can use the following option to group the commits by 'scope' instead of 'type' by default.

```json
{
  "plugins": {
    "@release-it/conventional-changelog": {
      "writerOpts": {
        "groupBy:" "scope"
      }
    }
  }
}
```

If you want to customize the templates used to write the changelog, you can do it like in a `.release-it.js` file like
so:

```js
const fs = require('fs');

const commitTemplate = fs.readFileSync('commit.hbs').toString();

module.exports = {
  plugins: {
    '@release-it/conventional-changelog': {
      writerOpts: {
        commitPartial: commitTemplate
      }
    }
  }
};
```

## Command-line

Options for this plugin can be set from the command line. Some examples:

```
release-it --plugins.@release-it/conventional-changelog.infile=history.md
release-it --no-plugins.@release-it/conventional-changelog.infile
```

- Keys are separated by dots.
- Values can be negated by prefixing the key with `no-`.
- Arguments may need to be single-quoted (`'`) such as `--'deep.key=value'` or `'--deep.key=value'`

Depending on your shell or OS this may differ.

## GitHub Actions

When using this plugin in a GitHub Action, make sure to set
[`fetch-depth: 0`](https://github.com/actions/checkout#fetch-all-history-for-all-tags-and-branches) so the history is
available to determine the correct recommended bump and changelog.

Also see https://github.com/release-it/release-it/blob/master/docs/ci.md#github-actions
