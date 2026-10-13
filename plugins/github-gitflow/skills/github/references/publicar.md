# Publicar um projeto no GitHub

Objetivo: transformar uma pasta local em um repositório no GitHub com histórico limpo.

## 1. Inicializar o git (se ainda não for repositório)

```bash
git init -b main
```

Se já for repositório, pule para o passo 3 e confira se já existe `remote` (`git remote -v`). Se existir, o projeto provavelmente já está publicado — basta commitar e dar push.

## 2. Criar o `.gitignore`

Identifique a stack pelos arquivos presentes e gere um `.gitignore` adequado. Base comum:

```gitignore
# Dependências e builds
node_modules/
dist/
build/
.next/
__pycache__/
*.pyc
.venv/
venv/
target/

# Ambiente e segredos
.env
.env.*
!.env.example
*.pem
*.key

# Sistema / editores
.DS_Store
Thumbs.db
.vscode/
.idea/

# Logs
*.log
```

Não ignore `.claude/` por padrão — ele pode conter skills e configurações do projeto que o usuário quer versionar. Pergunte se estiver em dúvida.

## 3. Revisar o que será commitado

```bash
git add -A
git status --short
```

Leia a lista. Procure segredos e arquivos grandes (>50 MB o GitHub rejeita; >100 MB bloqueia). Se aparecer algo suspeito, remova do stage (`git rm --cached <arquivo>`), ajuste o `.gitignore` e avise o usuário.

## 4. Primeiro commit

```bash
git commit -m "chore: commit inicial"
```

Se o git reclamar de identidade (`user.name`/`user.email`), peça ao usuário os dados ou sugira configurar com `git config --global`. Não invente nome/e-mail.

## 5. Criar o repositório e enviar

Nome padrão: nome da pasta, em minúsculas e com hífens. Confirme nome e visibilidade com o usuário antes de criar.

```bash
gh repo create <nome> --private --source=. --remote=origin --push --description "<descrição curta>"
```

- Use `--public` só se o usuário pedir (ou para GitHub Pages em conta gratuita).
- Se o nome já existir: `gh repo view <usuario>/<nome>` para conferir; ofereça outro nome ou usar o existente (`git remote add origin <url>` + `git push -u origin main`).

## 5b. Preparar o Git Flow

Logo após o primeiro push, crie a branch `develop` a partir de `main` e marque a primeira versão — a partir daqui o trabalho segue a skill `gitflow`:

```bash
git tag -a v1.0.0 -m "v1.0.0" && git push origin v1.0.0
git switch -c develop && git push -u origin develop
```

## 6. (Opcional) README

Se o Git Flow já estiver ativo, o README entra por uma `feature/readme` (veja a skill `gitflow`).

Se o projeto não tiver `README.md`, ofereça criar um curto: título, uma frase do que é, como rodar/abrir, tecnologias. Commit separado: `docs: adiciona README`.

## 7. Resultado

```bash
gh repo view --web   # só se o usuário quiser abrir no navegador
gh repo view --json url --jq .url
```

Informe a URL do repositório.
