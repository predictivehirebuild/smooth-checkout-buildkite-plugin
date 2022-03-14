# Smooth Checkout
All the things you need during a Buildkite checkout :butter: :kite:

## Usage

### Repository-less builds
```yml
steps:
  - command: echo "Skips checking out Git project in checkout" 
    plugins:
      - predictivehirebuild/smooth-checkout#v1.0.0:
          skip_checkout: true
```

### Checking out repo
```yml
steps:
  - command: echo "Checks out repo at given ref"
    plugins:
      - predictivehirebuild/smooth-checkout#v1.0.0:
          repos:
            - config:
              - url: git@github.com:<username>/<reponame>.git
                ref: <ref>
```

If `ref` is not provided the values of `BUILDKITE_BRANCH` and `BUILDKITE_COMMIT` env vars are used.

Allowed values for `ref`:
- Branch name
- Git tag
- Commit SHA (40 character long hash)

### Checking out multiple repositories
You can checkout multiple repositories by providing multiple `config` elements:
```yaml
steps:
  - command: echo "Checks out multiple git repositories"
    plugins:
      - predictivehirebuild/smooth-checkout#v1.0.0:
          repos:
            - config:
              - url: git@github.com:<username>/<repo_1>.git
            - config:
              - url: https://github.com/<username>/<repo_2>.git
                ref: <ref>
```
Unlike single repo checkouts, when checking out multiple repos, the working directory will be set to `$WOKRSPACE`, where all the repo checkouts have been done.
In the above example, the contents of the working directory would be `repo_1/` and `repo_2/`.

You can also explicitly provide the path to an ssh identity file using the `ssh_key_path` config field:
```yaml
steps:
  - command: echo "Checks out multiple git repositories"
    plugins:
      - predictivehirebuild/smooth-checkout#v1.0.0:
          repos:
            - config:
              - url: git@github.com:<username>/<repo_1>.git
                ssh_key_path: .ssh/key_1
            - config:
              - url: git@github.com:<username>/<repo_2>.git
                ref: <ref>
                ssh_key_path: .ssh/key_2
```

If you are using [smooth-secrets](https://github.com/predictivehirebuild/smooth-secrets-buildkite-plugin) to configure ssh keys, you can do the following to easily set the `ssh_key_path`:
```yaml
steps:
  - command: echo "Checks out multiple git repositories"
    plugins:
      - predictivehirebuild/smooth-checkout#v1.0.0:
          repos:
            - config:
              - url: git@github.com:<username>/<repo>.git
                ssh_key_path: $$SECRETS_DIR/<key>
```
where `<key>` is the value of [`key` field](https://github.com/predictivehirebuild/smooth-secrets-buildkite-plugin#key-required-string) in smooth-secrets config with any `/` characters replaced by `-`.
