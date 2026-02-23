# VaultSpectre Action

GitHub Action for [VaultSpectre](https://github.com/ppiankov/vaultspectre) — scan repositories for Vault secret references and validate them against a HashiCorp Vault instance.

## What it does

- Downloads VaultSpectre binary (auto-detects latest version)
- Scans your repository for Vault secret path references
- Validates each path against your Vault instance
- Optionally uploads SARIF results to GitHub Security tab
- Propagates exit codes for CI/CD gating

## What it does NOT do

- Does not read secret values — only validates path existence
- Does not manage or rotate secrets
- Does not require any Vault plugins or sidecars

## Usage

### Basic scan

```yaml
- uses: ppiankov/vaultspectre-action@v1
  with:
    vault-addr: ${{ secrets.VAULT_ADDR }}
    token: ${{ secrets.VAULT_TOKEN }}
```

### Fail on missing secrets

```yaml
- uses: ppiankov/vaultspectre-action@v1
  with:
    vault-addr: ${{ secrets.VAULT_ADDR }}
    token: ${{ secrets.VAULT_TOKEN }}
    fail-on-missing: 'true'
```

### SARIF upload to GitHub Security tab

```yaml
- uses: ppiankov/vaultspectre-action@v1
  with:
    vault-addr: ${{ secrets.VAULT_ADDR }}
    token: ${{ secrets.VAULT_TOKEN }}
    format: sarif
    upload-sarif: 'true'
```

### SpectreHub envelope output

```yaml
- uses: ppiankov/vaultspectre-action@v1
  with:
    vault-addr: ${{ secrets.VAULT_ADDR }}
    token: ${{ secrets.VAULT_TOKEN }}
    format: spectrehub
```

### With baseline

```yaml
- uses: ppiankov/vaultspectre-action@v1
  with:
    vault-addr: ${{ secrets.VAULT_ADDR }}
    token: ${{ secrets.VAULT_TOKEN }}
    baseline: .vaultspectre-baseline.json
    fail-on-missing: 'true'
```

### Pin to specific version

```yaml
- uses: ppiankov/vaultspectre-action@v1
  with:
    vault-addr: ${{ secrets.VAULT_ADDR }}
    token: ${{ secrets.VAULT_TOKEN }}
    version: '0.3.0'
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `vault-addr` | Yes | — | Vault server address |
| `token` | Yes | — | Vault authentication token |
| `repo-path` | No | `.` | Path to repository to scan |
| `format` | No | `text` | Output format: `text`, `json`, `sarif`, or `spectrehub` |
| `fail-on-missing` | No | `false` | Exit with error if missing secrets found |
| `stale-days` | No | `90` | Staleness threshold in days (0 to disable) |
| `baseline` | No | — | Path to baseline file for suppressing known findings |
| `version` | No | `latest` | VaultSpectre version (e.g., `0.3.0`) |
| `args` | No | — | Additional arguments passed to `vaultspectre scan` |
| `upload-sarif` | No | `false` | Upload SARIF to GitHub Security tab (requires `format: sarif`) |

## Outputs

| Output | Description |
|--------|-------------|
| `exit-code` | Exit code from `vaultspectre scan` (0=ok, 1=issues, 2=error) |
| `report-path` | Path to generated report file (json/sarif/spectrehub formats) |

## Exit codes

| Code | Meaning |
|------|---------|
| 0 | All secrets validated successfully |
| 1 | Issues found (missing/invalid secrets, requires `--fail-on-missing`) |
| 2 | Configuration or connection error |

## License

MIT
