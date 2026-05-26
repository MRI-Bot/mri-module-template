# mri-module-template

Repositório template para novos módulos `@mri-bot/*`.

---

## Criando um novo módulo

1. Clique em **Use this template → Create a new repository** no GitHub.
2. Nomeie o repo como `mri-<nome-do-modulo>`.
3. Clone o repo e substitua os placeholders no `package.json`:
   ```bash
   # substitui MODULE_NAME pelo nome real (ex: webhooks)
   sed -i 's/MODULE_NAME/webhooks/g' package.json
   ```
4. Adicione o novo repo na lista `repos` em `.github/sync.yml` **deste** template para receber atualizações automáticas.
5. Crie o secret `PACKAGES_TOKEN` no repo novo (Settings → Secrets → Actions).

---

## Arquivos compartilhados (sincronizados automaticamente)

| Arquivo | Descrição |
|---|---|
| `.gitignore` | Ignora `node_modules`, `dist`, `coverage` |
| `.npmrc` | Aponta `@mri-bot:*` para GitHub Packages |
| `tsconfig.json` | Configuração TypeScript base |
| `tsconfig.build.json` | TS build (exclui `.spec.ts`) |
| `tsconfig.test.json` | TS para Jest (`types: jest, node`) |
| `.github/workflows/ci.yml` | CI: typecheck + test:coverage |
| `.github/workflows/publish.yml` | Publish no push de tag `v*` |

Quando qualquer um desses arquivos mudar no template, o workflow
`template-sync.yml` abrirá PRs automáticos em todos os repos listados
em `.github/sync.yml`.

---

## Configurando o sync automático

O sync usa [`BetaHuhn/repo-file-sync-action`](https://github.com/BetaHuhn/repo-file-sync-action).

### Pré-requisito: secret `SYNC_TOKEN`

1. Crie um PAT em https://github.com/settings/tokens com escopo `repo`.
2. Adicione como secret `SYNC_TOKEN` neste repo template:
   `Settings → Secrets and variables → Actions → New repository secret`.

### Adicionando um novo consumer

Edite `.github/sync.yml` e adicione o repo na lista `repos`:

```yaml
repos: |
  MRI-Bot/mri-guards
  MRI-Bot/mri-fivem
  MRI-Bot/mri-novo-modulo   # ← adicionar aqui
```

### Trigger manual

Vá em **Actions → Sync template files → Run workflow** para forçar
a sincronização sem esperar um push.

---

## Estrutura recomendada do módulo

```
mri-<nome>/
├── src/
│   ├── <nome>.module.ts
│   ├── <nome>.service.ts
│   ├── <nome>.service.spec.ts
│   └── index.ts
├── .github/
│   └── workflows/         # gerenciados pelo template
├── .gitignore             # gerenciado pelo template
├── .npmrc                 # gerenciado pelo template
├── tsconfig.json          # gerenciado pelo template
├── tsconfig.build.json    # gerenciado pelo template
├── tsconfig.test.json     # gerenciado pelo template
└── package.json           # específico do módulo
```

> **Nota**: os arquivos "gerenciados pelo template" **não devem ser editados**
> diretamente nos repos consumers — edite aqui e deixe o sync propagar.
