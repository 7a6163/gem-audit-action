# gem-audit-action

GitHub Action to audit `Gemfile.lock` for vulnerable gem versions and insecure
sources using [gem-audit].

## Usage

### Basic

```yaml
name: Security Audit
on: [push, pull_request]
jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: 7a6163/gem-audit-action@v1
```

### Severity threshold

Only fail on high and critical vulnerabilities:

```yaml
      - uses: 7a6163/gem-audit-action@v1
        with:
          severity: high
```

### Strict mode with fresh database

```yaml
      - uses: 7a6163/gem-audit-action@v1
        with:
          strict: true
          max-db-age: 7
          fail-on-stale: true
```

### JSON report as artifact

```yaml
      - uses: 7a6163/gem-audit-action@v1
        with:
          format: json
          output: audit-report.json
        continue-on-error: true

      - uses: actions/upload-artifact@v4
        with:
          name: audit-report
          path: audit-report.json
```

### Pin a specific version

```yaml
      - uses: 7a6163/gem-audit-action@v1
        with:
          version: '2.1.0'
```

### Monorepo with custom path

```yaml
      - uses: 7a6163/gem-audit-action@v1
        with:
          working-directory: services/api
          gemfile-lock: Gemfile.lock
```

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `version` | gem-audit version to use | `latest` |
| `severity` | Minimum severity: `none`, `low`, `medium`, `high`, `critical` | _(all)_ |
| `strict` | Treat parse/load warnings as errors | `false` |
| `max-db-age` | Maximum advisory database age in days | _(disabled)_ |
| `fail-on-stale` | Exit with code 3 if database is stale | `false` |
| `gemfile-lock` | Path to the Gemfile.lock file | `Gemfile.lock` |
| `ignore` | Space-separated advisory IDs to ignore | _(none)_ |
| `format` | Output format: `text` or `json` | `text` |
| `output` | Write output to a file | _(stdout)_ |
| `working-directory` | Project directory to audit | `.` |
| `quiet` | Suppress output | `false` |
| `verbose` | Show detailed advisory descriptions | `false` |

## Outputs

| Output | Description |
|--------|-------------|
| `exit-code` | Exit code from gem-audit: `0` = clean, `1` = vulnerable, `2` = error, `3` = stale |

## Exit Codes

| Code | Meaning |
|------|---------|
| `0` | No vulnerabilities found |
| `1` | Vulnerabilities found |
| `2` | Tool error or `--strict` violation |
| `3` | Advisory database is stale (`--fail-on-stale`) |

## Supported Platforms

| Runner | Architecture |
|--------|-------------|
| `ubuntu-latest` | x86_64, ARM64 |
| `macos-latest` | x86_64, ARM64 |
| `windows-latest` | x86_64 |

## License

MIT

[gem-audit]: https://github.com/7a6163/gem-audit
