# go-fuzz-action
GitHub Action for Go fuzz testing. This Action runs Go's built-in fuzz testing, added in Go 1.18, on your code.


## Do you find this useful?

You can sponsor me [here](https://github.com/sponsors/jidicula)!

## Inputs
* `fuzz-time` [**REQUIRED**]: Fuzz target iteration duration, specified as a `time.Duration` (for example `1h30s`). Corresponds to `-fuzztime` flag for the `go test` command. **Ensure this is less than your job/workflow timeout**.
* `packages` [optional]: Run fuzz test on these packages. Corresponds to the `[packages]` input for the `go test` command.
  * Default: `.`
* `fuzz-regexp` [optional]: Run the fuzz test matching the regular expression. Corresponds to the `-fuzz` flag for the `go test` command.
  * Default: `Fuzz`
* `fuzz-minimize-time` [optional]: Fuzz minimization duration, specified as a `time.Duration` (for example `1h30s`). Corresponds to `-fuzzminimizetime` flag for the `go test` command. If you provide this input, ensure it is less than your job timeout.
  * Default: `10s`
  
## Returns:
* SUCCESS: if your fuzz tests don't raise a failure within the `fuzz-time` input constraint.
* FAILURE: if your fuzz tests raise a failure within the `fuzz-time` input constraint.
  * The workflow run logs will include instructions on how to download (using the [GitHub CLI](https://cli.github.com)) the failing seed corpus to your local machine for remediation, regardless of run trigger.
  * If you run this Action in a PR workflow, it'll comment these instructions on your PR: <img width="864" alt="image" src="https://user-images.githubusercontent.com/12765385/162630131-9050221e-dc75-4620-aa19-d004d8678074.png">

# Usage
⚠️This Action is not tested on `windows` GitHub Actions runners! Use with `windows` runner OS at your own risk!

Create a `.github/workflows/go-fuzz-test.yml` in your repository containing:


```yaml
name: Go fuzz test
on:
  push:
  pull_request:
jobs:
  fuzz-test:
    name: Fuzz test
    runs-on: ubuntu-latest
    steps:
      - uses: jidicula/go-fuzz-action@v1.1.0
        with:
          fuzz-time: 30s
```

## Fuzz test all packages in repo

If you have multiple packages in your repo and you want to fuzz test them all, create a `.github/workflows/go-fuzz-test.yml` in your repository containing:

```yaml
name: Go fuzz test
on:
  push:
  pull_request:
jobs:
  fuzz-test:
    name: Fuzz test
    runs-on: ubuntu-latest
    steps:
      - uses: jidicula/go-fuzz-action@v1.1.0
        with:
          packages: './...'
          fuzz-time: 30s
```

## Longer minimize time

If you want the fuzz test to spend more time on minimizing the failing input to the smallest possible and most human readable value which will still produce an error, create a `.github/workflows/go-fuzz-test.yml` in your repository containing:

```yaml
name: Go fuzz test
on:
  push:
  pull_request:
jobs:
  fuzz-test:
    name: Fuzz test
    runs-on: ubuntu-latest
    steps:
      - uses: jidicula/go-fuzz-action@v1.1.0
        with:
          packages: './...'
          fuzz-time: 30s
          fuzz-minimize-time: 1m
```

## Fuzz tests with different regexp

If you have fuzz tests that don't begin with `Fuzz` (the default regexp), create a `.github/workflows/go-fuzz-test.yml` in your repository containing:

```yaml
name: Go fuzz test
on:
  push:
  pull_request:
jobs:
  fuzz-test:
    name: Fuzz test
    runs-on: ubuntu-latest
    steps:
      - uses: jidicula/go-fuzz-action@main
        with:
          packages: './...'
          fuzz-time: 30s
          fuzz-minimize-time: 1m
          fuzz-regexp: OtherFuzzRegexp
```

# Example repo

I haven't figured out how to test this adequately within this repo, so you can verify its behaviour here: https://github.com/jidicula/test-go-fuzz-action/pull/2

# Who uses this?

[These public repos](https://github.com/search?o=desc&q=uses%3A+jidicula%2Fgo-fuzz-action+-user%3Ajidicula&s=indexed&type=Code) use this Action.
