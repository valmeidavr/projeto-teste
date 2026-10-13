---
name: github
description: "Trabalha com Git e GitHub pelo terminal (git + GitHub CLI gh) - publicar um projeto local no GitHub (git init, .gitignore, primeiro commit, criar repositório, push), fazer deploy de sites estáticos no GitHub Pages, criar branches, commits padronizados e pull requests, e gerenciar issues e releases/changelog. Use esta skill sempre que o usuário mencionar GitHub, repositório, repo, git, commit, push, branch, PR, pull request, GitHub Pages, 'subir o projeto', 'colocar no ar', 'publicar o site', issue, release, tag ou versão - mesmo que não diga GitHub explicitamente (por exemplo 'salva isso no git' ou 'quero um link público pra minha landing page')."
---

# GitHub

Skill para levar um projeto do disco local até o GitHub e mantê-lo lá: publicação, GitHub Pages, fluxo de commits/PRs, issues e releases. Usa `git` e a GitHub CLI (`gh`).

## Antes de tudo: verificar o ambiente

Rode uma vez no início da tarefa:

```bash
git --version
gh --version
gh auth status
git rev-parse --is-inside-work-tree 2>/dev/null && git status -sb && git remote -v
```

- Se `gh` não estiver instalado: no Windows `winget install --id GitHub.cli`, no macOS `brew install gh`. Peça ao usuário para instalar — não instale sem permissão.
- Se `gh auth status` falhar: peça ao usuário para rodar `gh auth login` (é interativo, você não consegue rodar por ele).
- Descubra o usuário logado com `gh api user --jq .login` — use-o como dono padrão dos repositórios.

## Padrão de branches: Git Flow

Os repositórios seguem **Git Flow**. Antes de criar branch, commitar, fazer push, abrir/mesclar PR, publicar versão ou fazer deploy, carregue e siga a skill `gitflow` deste mesmo plugin. Onde os guias abaixo mostram comandos de branch/merge, prevalece o que a skill `gitflow` define — em especial: nada de commit direto em `main`/`develop`, features saem de `develop`, e deploy é release (ou hotfix) mesclada em `main`.

## Escolha o guia certo

Leia **apenas** o guia da tarefa em questão:

| Pedido do usuário | Guia |
|---|---|
| Subir/publicar um projeto, criar repositório, primeiro push | [references/publicar.md](references/publicar.md) |
| Colocar um site no ar, link público, GitHub Pages | [references/pages.md](references/pages.md) (exige repositório publicado — se ainda não existe, siga `publicar.md` antes) |
| Commit, branch, pull request, revisar/mesclar PR | [references/commits-prs.md](references/commits-prs.md) |
| Issues, releases, tags, versões, changelog | [references/issues-releases.md](references/issues-releases.md) |

Pedidos compostos ("sobe e coloca no ar") encadeiam guias na ordem da tabela.

## Regras de segurança (valem para todos os guias)

Ações no GitHub são públicas ou difíceis de desfazer, então:

1. **Confirme antes de ações externas** — criar repositório, push, tornar algo público, ativar Pages, mesclar PR, publicar release. Mostre em uma frase o que vai acontecer (nome do repo, visibilidade, branch) e aguarde o "ok", a menos que o usuário já tenha pedido explicitamente aquela ação.
2. **Visibilidade padrão é privada.** Só crie repositório público se o usuário pedir ou se for necessário para o GitHub Pages em conta gratuita (avise disso).
3. **Procure segredos antes do primeiro commit**: arquivos `.env`, chaves (`*.pem`, `id_rsa`), `credentials.json`, tokens no código. Se encontrar, adicione ao `.gitignore` e avise o usuário — segredo enviado ao GitHub deve ser considerado vazado, mesmo se apagado depois.
4. **Nunca** use `push --force`, `reset --hard`, apagar branches remotas ou reescrever histórico já publicado sem pedido explícito. Se precisar, prefira `--force-with-lease` e explique o motivo.
5. **Não pule hooks** (`--no-verify`). Se um hook falhar, investigue e corrija.
6. Mensagens de commit e descrições de PR seguem as instruções de atribuição do sistema/usuário, quando houver.

## Ao terminar

Informe ao usuário de forma objetiva: o que foi feito, as URLs relevantes (repositório, Pages, PR, release) e qualquer pendência (ex.: "o Pages leva 1–2 minutos para ficar disponível").
