# Issues e releases

## Issues

### Listar e consultar

```bash
gh issue list                         # abertas
gh issue list --state all --label bug --assignee @me
gh issue view <n> --comments
```

### Criar

Escreva o corpo em arquivo temporário e crie:

```bash
gh issue create --title "<título claro>" --body-file <arquivo> --label <label>
```

Modelos de corpo:

**Bug**
```markdown
## Descrição
## Passos para reproduzir
1.
## Comportamento esperado
## Comportamento atual
## Ambiente (navegador/SO/versão)
```

**Funcionalidade**
```markdown
## Problema / motivação
## Proposta
## Critérios de aceite
- [ ]
```

Se a label não existir, `gh issue create` falha — crie com `gh label create <nome> --color <hex>` ou omita a label. Ao criar várias issues de uma vez (ex.: a partir de uma lista de TODOs), mostre a lista ao usuário antes.

### Atualizar / fechar

```bash
gh issue comment <n> -b "..."
gh issue edit <n> --add-label <l> --add-assignee @me
gh issue close <n> --reason completed   # ou "not planned"
```

Prefira fechar issues pelo PR que as resolve (`Closes #<n>` no corpo do PR).

## Releases

### Escolher a versão (SemVer `vMAJOR.MINOR.PATCH`)

Veja a última: `gh release list --limit 5` ou `git describe --tags --abbrev=0`.

- `fix` apenas → PATCH (v1.2.3 → v1.2.4)
- algum `feat` → MINOR (v1.2.3 → v1.3.0)
- mudança incompatível (`!` no tipo ou `BREAKING CHANGE`) → MAJOR (v1.2.3 → v2.0.0)
- primeira release → `v1.0.0` se estável, `v0.1.0` se experimental

Sugira a versão e confirme com o usuário.

### Gerar o changelog

```bash
git log <ultima-tag>..HEAD --pretty=format:"%s (%h)" --no-merges
```

Agrupe por tipo:

```markdown
## Novidades
- ...
## Correções
- ...
## Outras mudanças
- ...
```

Omita seções vazias e commits irrelevantes para quem usa (ex.: `chore: ajusta lint`). Se o projeto mantém `CHANGELOG.md`, adicione a nova seção no topo e commite (`docs: atualiza changelog para vX.Y.Z`) antes de criar a release.

Alternativa automática: `gh release create <tag> --generate-notes` (usa os PRs mesclados).

### Publicar

```bash
gh release create v1.3.0 --title "v1.3.0" --notes-file <arquivo>
```

- `--draft` para revisar antes de publicar; `--prerelease` para betas/RCs.
- Anexe arquivos de build passando os caminhos no final: `gh release create v1.3.0 dist/app.zip --notes-file ...`.
- A tag é criada a partir da branch padrão; use `--target <branch>` se for outra.

Informe a URL da release ao terminar.
