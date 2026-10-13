# Deploy no GitHub Pages

Objetivo: colocar um site estático (HTML/CSS/JS, ou o build de um framework) em `https://<usuario>.github.io/<repo>/`.

## Pré-requisitos

- O repositório já existe no GitHub com o código enviado (senão, siga `publicar.md`).
- **Conta gratuita só publica Pages de repositório público.** Se o repo for privado, avise o usuário e pergunte se pode torná-lo público (`gh repo edit --visibility public --accept-visibility-change-consequences`). Em planos pagos, privado funciona.
- Descubra dono e nome: `gh repo view --json owner,name --jq '.owner.login + "/" + .name'`.

## Caso A — site estático puro (há `index.html` na raiz ou em `/docs`)

Publica direto da branch, sem build:

```bash
gh api -X POST repos/<owner>/<repo>/pages \
  -f "source[branch]=main" -f "source[path]=/"
```

Use `source[path]=/docs` se o site estiver em `docs/`. Se o Pages já estiver ativo (erro 409), atualize com `-X PUT` nos mesmos campos.

Adicione um arquivo vazio `.nojekyll` na raiz se houver pastas/arquivos começando com `_` (o Jekyll os ignora).

## Caso B — projeto com build (Vite, React, Next export, Astro…)

Use GitHub Actions. Crie `.github/workflows/pages.yml`:

```yaml
name: Deploy Pages
on:
  push:
    branches: [main]
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: pages
  cancel-in-progress: true
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist   # ajuste: dist, build, out...
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

Ative o Pages no modo Actions:

```bash
gh api -X POST repos/<owner>/<repo>/pages -f build_type=workflow
```

**Caminho base:** o site fica em `/<repo>/`, não na raiz. Ajuste o framework (ex.: Vite `base: '/<repo>/'`, Next `basePath`), senão CSS/JS dão 404. Sites estáticos puros com caminhos relativos (`style.css`, não `/style.css`) funcionam sem ajuste — confira isso no HTML.

Faça commit e push do workflow, depois acompanhe:

```bash
gh run watch
```

## Verificar

```bash
gh api repos/<owner>/<repo>/pages --jq '.html_url + " (" + .status + ")"'
```

A primeira publicação leva de 1 a 2 minutos. Informe a URL ao usuário e avise sobre esse tempo. Se o usuário quiser, adicione a URL ao repositório: `gh repo edit --homepage <url>`.

## Problemas comuns

- **404 no site:** aguarde alguns minutos; confira se `index.html` está no caminho configurado; confira o nome do arquivo (maiúsculas importam).
- **Sem estilo/scripts:** caminhos absolutos (`/style.css`) ou `base` do framework errado.
- **Erro 422 ao ativar:** repositório privado em conta gratuita, ou branch inexistente.
