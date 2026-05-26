# mri-module-template

Repositório template para novos módulos `@mri-bot/*`.

---

## Setup inicial (único, já feito)

O sync automático exige um secret `SYNC_TOKEN` **neste repo template**.

### Criando o `SYNC_TOKEN`

1. Acesse https://github.com/settings/tokens?type=beta *(fine-grained)* ou  
   https://github.com/settings/tokens *(classic)*

2. **Classic token** (mais simples):
   - Clique em **Generate new token (classic)**
   - Note: `mri-module-template sync`
   - Expiration: sem prazo ou 1 ano
   - Scopes: marque **`repo`** (controle total de repos privados/públicos)
   - Clique em **Generate token** e copie o valor

3. Adicione o token como secret neste repo:  
   **[Settings → Secrets and variables → Actions → New repository secret](https://github.com/MRI-Bot/mri-module-template/settings/secrets/actions/new)**
   - Name: `SYNC_TOKEN`
   - Secret: cole o token gerado

> O token precisa ter acesso de **escrita** nos repos consumers listados em `.github/sync.yml`.  
> Se os repos forem privados, o owner do token deve ser membro da org `MRI-Bot` com permissão de push.

---

## Criando um novo módulo

1. Clique em **[Use this template → Create a new repository](https://github.com/new?template_name=mri-module-template&template_owner=MRI-Bot)**.
2. Nomeie o repo como `mri-<nome-do-modulo>` dentro da org `MRI-Bot`.
3. Clone o repo e substitua o placeholder no `package.json`:
   ```bash
   # substitui MODULE_NAME pelo nome real (ex: webhooks)
   sed -i 's/MODULE_NAME/webhooks/g' package.json
   ```
4. Adicione o novo repo na lista `repos` em [`.github/sync.yml`](.github/sync.yml) **deste** template:
   ```yaml
   repos: |
     MRI-Bot/mri-guards
     MRI-Bot/mri-fivem
     MRI-Bot/mri-novo-modulo   # ← adicionar aqui
   ```
5. No repo novo: crie o secret `PACKAGES_TOKEN` (Settings → Secrets → Actions) com um PAT de escopo `read:packages`.

---

## Arquivos compartilhados

Estes arquivos são gerenciados aqui e **não devem ser editados diretamente nos repos consumers** — edite no template e o sync propaga via PR.

| Arquivo | Descrição |
|---|---|
| `.gitignore` | Ignora `node_modules`, `dist`, `coverage` |
| `.npmrc` | Aponta `@mri-bot:*` para GitHub Packages |
| `tsconfig.json` | Configuração TypeScript base |
| `tsconfig.build.json` | TS build (exclui `.spec.ts`) |
| `tsconfig.test.json` | TS para Jest (`types: jest, node`) |
| `.github/workflows/ci.yml` | CI: typecheck + test:coverage no Ubuntu |
| `.github/workflows/publish.yml` | Publish no push de tag `v*` |

---

## Como funciona o sync automático

Ao fazer push no `main` com alterações em qualquer arquivo acima, o workflow
[`template-sync.yml`](.github/workflows/template-sync.yml) usa
[`BetaHuhn/repo-file-sync-action`](https://github.com/BetaHuhn/repo-file-sync-action)
para abrir PRs automáticos em cada repo listado em `.github/sync.yml`.

Cada PR tem o label `template-sync` e pode ser mergeado normalmente.

### Trigger manual

Vá em **[Actions → Sync template files → Run workflow](https://github.com/MRI-Bot/mri-module-template/actions/workflows/template-sync.yml)**
para forçar a sincronização sem alterar nenhum arquivo.

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
│   └── workflows/         # gerenciados pelo template — não editar
├── .gitignore             # gerenciado pelo template — não editar
├── .npmrc                 # gerenciado pelo template — não editar
├── tsconfig.json          # gerenciado pelo template — não editar
├── tsconfig.build.json    # gerenciado pelo template — não editar
├── tsconfig.test.json     # gerenciado pelo template — não editar
└── package.json           # específico do módulo ✏️
```
