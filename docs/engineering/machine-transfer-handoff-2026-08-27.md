---
title: Handoff para retomada do Sandicts em outra máquina
doc-type: operational-handoff
status: active
prepared-on: 2026-08-27
primary-jira: KAN-27
---

# Handoff para retomada do Sandicts em outra máquina

## Estado corrente

| Repositório | Referência preservada | Estado |
| --- | --- | --- |
| `reactjs-sandicts-web` | `feature/KAN-105-magic-link-sign-in` em `f8aaa2e` | PR `#74` aberta contra `developer`; KAN-105 em `In Review` |
| `nodejs-sandicts-api` | `feature/KAN-86-google-sign-in` em `ec5f4ae` | checkout limpo e branch remota sincronizada |
| `sandicts-docs` | `docs/KAN-27-machine-handoff-2026-08-27` | branch criada para preservar documentação e instruções portáteis |

O próximo código planejado, depois do merge e encerramento da KAN-105, é a
KAN-88 para proteger rotas e preservar retorno pós-login. Não iniciar essa
issue a partir de branch antiga: atualizar `developer` antes de criar a branch.

## Material privado fora do Git

O pacote `SANDICTS_TRANSFER_2026-08-27` deve conter, sem registrar valores
neste documento:

- environments locais da API e do web;
- configuração local selecionada da Vercel;
- dump SQL do PostgreSQL de desenvolvimento;
- bundles Git completos dos três repositórios;
- snapshot do worklog e skills pessoais;
- configuração portátil do workspace e checksums SHA-256.

O arquivo ZIP contém dados sensíveis. Ele deve ser copiado somente para mídia
criptografada e removido depois que a restauração for comprovada.

## Restauração resumida

1. Instalar Git, GitHub CLI, Node.js 24, npm 11, Docker Desktop, VS Code e Codex.
2. Clonar os repositórios oficiais ou restaurar os bundles do pacote.
3. Executar `tooling/workspace/install-workspace-config.ps1` a partir do
   repositório de docs.
4. Recolocar os environments somente em seus destinos locais ignorados pelo
   Git e autenticar novamente os provedores externos.
5. Subir PostgreSQL e Mailpit, restaurar o dump e executar `npm ci` nos dois
   aplicativos.
6. Validar API, web, magic link, Google Sign-In e One Tap antes de desativar a
   máquina anterior.

## Segurança

- Nunca commitar `.env`, Client Secret, credenciais de banco ou tokens.
- Recriar o `VERCEL_OIDC_TOKEN`, pois ele é temporário.
- Não copiar o diretório global `.codex`; instalar o Codex, autenticar novamente
  e restaurar apenas skills pessoais e instruções portáteis.
- Manter a máquina anterior e o pacote de 2026-08-21 até a nova restauração ser
  validada.
