# github-gitflow

Plugin do Claude Code com duas skills:

- **github** — publicar projetos no GitHub, ativar o GitHub Pages, commits padronizados (Conventional Commits), pull requests, issues e releases, usando `git` e a GitHub CLI (`gh`).
- **gitflow** — padrão de branches Git Flow (`main`, `develop`, `feature/*`, `release/*`, `hotfix/*`). A skill `github` sempre segue este padrão.

## Instalação

```
/plugin marketplace add valmeidavr/projeto-teste
/plugin install github-gitflow@valmeidavr
```

## Requisitos

- `git`
- GitHub CLI (`gh`) autenticada: `gh auth login`

## Exemplos de uso

- "sobe esse projeto pro GitHub e coloca no ar"
- "muda o título da página e faz o deploy" → feature → develop → release → main
- "o site está com erro no menu, corrige" → hotfix
