# Commits, branches e pull requests

## Commits

### Mensagens (Conventional Commits)

Formato: `<tipo>(<escopo opcional>): <resumo no imperativo, minúsculo, sem ponto>`

| Tipo | Quando |
|---|---|
| `feat` | nova funcionalidade |
| `fix` | correção de bug |
| `docs` | só documentação |
| `style` | formatação, sem mudar lógica |
| `refactor` | reestruturação sem mudar comportamento |
| `perf` | desempenho |
| `test` | testes |
| `chore` | build, deps, configs |
| `ci` | pipelines |

Exemplos:
- `feat(contato): adiciona link do WhatsApp`
- `fix(nav): corrige menu mobile que não fechava ao clicar`
- `docs: atualiza instruções de instalação`

Use o idioma que o repositório já usa (`git log --oneline -10`); na dúvida, o idioma do usuário. Se o histórico seguir outro padrão, siga o padrão existente.

Se a mudança for grande, adicione corpo explicando o **porquê** (linha em branco após o título).

### Como commitar

1. `git status` e `git diff` — entenda o que mudou.
2. Agrupe por assunto: mudanças sem relação viram commits separados (`git add <arquivos>` em vez de `git add -A`).
3. Não commite segredos nem arquivos gerados (veja `.gitignore`).
4. Commit e, se o usuário pedir, push.

Commite só quando o usuário pedir. Push só quando o usuário pedir ou quando fizer parte de um pedido como "abre um PR".

## Branches

Se estiver na branch padrão (`main`/`master`) e for fazer uma mudança que vai virar PR, crie uma branch antes:

```bash
git switch -c <tipo>/<descricao-curta>    # ex.: feat/secao-depoimentos, fix/menu-mobile
```

Atualizar com a principal:

```bash
git fetch origin
git rebase origin/main      # branch local ainda não compartilhada
git merge origin/main       # branch já usada por outras pessoas
```

Em conflito: mostre os arquivos ao usuário, resolva preservando a intenção dos dois lados, e confirme antes de concluir.

## Pull requests

### Abrir

```bash
git push -u origin HEAD
gh pr create --title "<título no formato de commit>" --body-file <arquivo>
```

Corpo do PR (escreva em arquivo temporário para preservar formatação):

```markdown
## O que muda
- ...

## Por quê
...

## Como testar
1. ...
```

Opções úteis: `--draft` (rascunho), `--reviewer <usuario>`, `--label <label>`, `--base <branch>`. Se houver issue relacionada, inclua `Closes #<n>` no corpo.

Informe a URL do PR ao terminar.

### Acompanhar e revisar

```bash
gh pr status
gh pr view <n> --comments
gh pr diff <n>
gh pr checks <n>        # status do CI
gh pr checkout <n>      # baixar o PR localmente
```

Para revisar o PR de outra pessoa: leia o diff, rode localmente se fizer sentido, e só poste comentários/aprovação (`gh pr review <n> --comment|--approve|--request-changes -b "..."`) com o "ok" do usuário — isso aparece publicamente com o nome dele.

### Mesclar

Confirme com o usuário e verifique que o CI passou (`gh pr checks`):

```bash
gh pr merge <n> --squash --delete-branch
```

Use `--merge` ou `--rebase` se o repositório tiver essa convenção (veja PRs anteriores). Depois: `git switch main && git pull`.
