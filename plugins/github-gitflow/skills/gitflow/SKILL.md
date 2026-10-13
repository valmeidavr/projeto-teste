---
name: gitflow
description: "Padrão de branches Git Flow (main, develop, feature/*, release/*, hotfix/*) para qualquer trabalho com git ou GitHub. Use esta skill sempre que for criar branch, commitar, fazer push, abrir/mesclar pull request, publicar versão, fazer deploy ou corrigir algo em produção - e também sempre que a skill github for usada, mesmo que o usuário não diga 'gitflow' (por exemplo 'sobe essa alteração', 'faz o deploy', 'corrige isso no site')."
---

# Git Flow

Os repositórios usam Git Flow. Toda mudança entra por uma branch de trabalho e chega à produção apenas por release ou hotfix. Assim `main` sempre reflete o que está no ar (quando há GitHub Pages, ele publica a partir dela) e `develop` acumula o que vai para a próxima versão.

Use `git` puro e `gh`. A extensão `git flow` não é necessária: os comandos abaixo funcionam em qualquer máquina.

## Branches

| Branch | Sai de | Volta para | Para quê |
|---|---|---|---|
| `main` | — | — | Produção. Cada merge recebe uma tag de versão. Nunca commite direto. |
| `develop` | `main` | — | Integração da próxima versão. Nunca commite direto. |
| `feature/<nome>` | `develop` | `develop` | Funcionalidades e mudanças comuns |
| `release/<versão>` | `develop` | `main` **e** `develop` | Preparar uma versão: ajustes finais, changelog |
| `hotfix/<versão>` | `main` | `main` **e** `develop` | Correção urgente do que está em produção |

Nomes em minúsculas com hífens: `feature/secao-depoimentos`, `release/1.2.0`, `hotfix/1.2.1`.

## 0. Garantir a estrutura (uma vez por repositório)

```bash
git fetch origin
git branch -a
```

Se `develop` não existir, crie a partir de `main` e envie (confirme com o usuário, é um push):

```bash
git switch main && git pull
git switch -c develop
git push -u origin develop
```

Se houver alterações não commitadas em `main` ou `develop`, leve-as para uma feature antes de commitar: `git switch -c feature/<nome>` mantém as mudanças do working tree.

## 1. Feature (mudança comum)

```bash
git switch develop && git pull
git switch -c feature/<nome>
# ... alterações + commits (Conventional Commits, ver skill github)
git push -u origin feature/<nome>
gh pr create --base develop --title "<feat: ...>" --body-file <arquivo>
```

Ao concluir (com "ok" do usuário, ou se ele já pediu para entregar a mudança):

```bash
gh pr merge <n> --merge --delete-branch     # preserva o histórico da feature
git switch develop && git pull
```

## 2. Release (levar develop para produção)

É assim que se faz o "deploy": o site só muda quando `main` muda.

1. Escolha a versão pelo SemVer a partir da última tag (`git describe --tags --abbrev=0`); sem tags, comece em `1.0.0`. Regras no guia `issues-releases.md` da skill github.
2. Crie a release:

```bash
git switch develop && git pull
git switch -c release/<versão>
# ajustes finais: número de versão, CHANGELOG.md -> commit "chore(release): <versão>"
git push -u origin release/<versão>
```

3. Mescle em `main` e marque a versão:

```bash
gh pr create --base main --head release/<versão> --title "release: <versão>" --body-file <changelog>
gh pr merge <n> --merge
git switch main && git pull
git tag -a v<versão> -m "v<versão>"
git push origin v<versão>
gh release create v<versão> --title "v<versão>" --notes-file <changelog>
```

4. Leve a release de volta para `develop` e apague a branch:

```bash
git switch develop && git pull
git merge --no-ff main -m "chore: merge release <versão> em develop"
git push
git push origin --delete release/<versão>
git branch -d release/<versão>
```

## 3. Hotfix (corrigir produção com urgência)

Use quando algo **no ar** está errado e não pode esperar a próxima release. Incremente o PATCH.

```bash
git switch main && git pull
git switch -c hotfix/<versão>
# correção + commit "fix: ..."
git push -u origin hotfix/<versão>
```

Depois, siga os passos 3 e 4 da release trocando `release/` por `hotfix/` (PR para `main`, tag, `gh release`, merge de volta em `develop`). Se existir uma `release/*` aberta, mescle o hotfix nela em vez de em `develop`.

## Escolhendo o fluxo a partir do pedido

| O usuário diz | Fluxo |
|---|---|
| "adiciona/muda X" (sem falar em publicar) | feature, termina com merge em `develop` |
| "muda X e faz o deploy" / "sobe pro site" | feature → merge em `develop` → release |
| "o site está com erro, corrige" | hotfix |
| "publica o que está em develop" / "lança versão" | release |

Para o pedido composto, informe o plano em uma linha antes de começar (por exemplo "vou criar `feature/contato`, mesclar em develop e publicar a release 1.1.0") — o usuário entende o que vai acontecer e pode pedir só parte disso.

## Regras

- Nada de commit direto em `main` ou `develop`. Se já houver commit local nelas sem push, mova-o para uma feature: `git switch -c feature/<nome>`, depois volte a branch original para `origin/<branch>` com `git branch -f <branch> origin/<branch>`. Nunca reescreva o que já foi enviado.
- Merges de release/hotfix usam merge commit (`--merge` / `--no-ff`), não squash, para que `main` e `develop` compartilhem o histórico e a volta para `develop` não gere conflitos falsos.
- Toda entrada em `main` ganha tag `vX.Y.Z` e uma GitHub Release.
- As regras de segurança da skill github continuam valendo (confirmar ações externas, sem force push, sem pular hooks).
- Ao terminar, informe: branches criadas/mescladas, versão e tag, URLs de PRs e release e, se `main` mudou, que o Pages atualiza em 1–2 minutos.
