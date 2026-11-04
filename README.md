# ivndv/.github

Config central compartida entre repos.

## Contenido

| Archivo | Para qué |
|---|---|
| `gitleaks.toml` | Reglas y allowlist comunes de gitleaks |
| `.github/workflows/gitleaks.yml` | Workflow reutilizable de escaneo de secretos |

## Uso del workflow

En cualquier repo, añadir al `ci-cd.yml`:

```yaml
jobs:
  gitleaks:
    uses: ivndv/.github/.github/workflows/gitleaks.yml@main
```

Y si el job de deploy depende de los checks:

```yaml
  deploy:
    needs: [lint, test, gitleaks]
```

Escanea solo los commits nuevos del push o PR. Para barrer todo el historial
de forma puntual:

```yaml
  gitleaks:
    uses: ivndv/.github/.github/workflows/gitleaks.yml@main
    with:
      full-history: true
```

## Uso local

```bash
git clone https://github.com/ivndv/.github.git ~/dotgithub
~/.local/bin/gitleaks git . --config ~/dotgithub/gitleaks.toml --redact -v --log-opts=--all
```

## Hook local (pre-commit)

El hook vive en `~/.githooks/pre-commit` y se activa con:

```bash
git config --global core.hooksPath ~/.githooks
```

Se salta con `git commit --no-verify`.

## Falsos positivos

Si gitleaks marca algo que no es un secreto, añadirlo a `gitleaks.toml` en
`[[allowlists]]` con el path o regex concreto. No añadir repos enteros.
