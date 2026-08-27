---
title: Plano de conclusão das pendências atuais do Sandicts
doc-type: execution-plan
status: superseded
planned-on: 2026-08-23
superseded-on: 2026-08-27
superseded-by: machine-transfer-handoff-2026-08-27.md
scope: authentication, closed-beta, deployment, repository-hygiene
primary-jira: KAN-27
---

# Sandicts — plano de conclusão das pendências atuais

## Status do documento

> Este arquivo preserva a fotografia e a ordem propostas em 2026-08-23. O
> estado corrente para retomada em outra máquina está em
> `machine-transfer-handoff-2026-08-27.md`; ele prevalece quando houver
> divergência de status, branch ou próxima tarefa.

- **Escopo:** concluir autenticação e Beta fechado zero-cost antes de preparar outra migração de máquina.
- **Repositórios:** `sandicts-docs`, `reactjs-sandicts-web` e `nodejs-sandicts-api`.
- **Branch de documentação atual:** `docs/KAN-27-preserve-engineering-docs`.
- **Status operacional:** Passo 0 parcialmente concluído; branches alinhadas,
  fluxo Jira publicado com Backlog nativo e somente KAN-27 em progresso, e
  KAN-85 mantida em revisão por gates locais pendentes.
- **Status de código copiar/colar:** bloqueado; cada issue de implementação ainda precisa ser executada sobre a versão atual de `developer`, uma por vez.
- **Evidências:** Git local/remoto em 2026-08-23, Jira Sandicts, PR web `#70`, diário operacional e `auth-local-preview-production-rollout-plan.md`.
- **Fora de escopo:** KAN-28/Produção, KAN-157/Netlify cancelada, expansão do restante do MVP, planos pagos e mudança de máquina durante esta sequência.

## Objetivo

Encerrar a frente realmente ativa do Sandicts sem confundir backlog futuro com
trabalho iniciado. O resultado esperado é um Beta fechado com autenticação
Google e magic link, acesso restrito a convidados, deploy reproduzível em
Vercel + Render + Neon e evidências suficientes para concluir KAN-27.

## Decisão sobre os commits de preservação

Os commits abaixo devem ser **mantidos**:

| Commit | Conteúdo | Decisão |
| --- | --- | --- |
| `09efcc3` | plano de autenticação e relatórios técnicos | Manter; os arquivos são documentação útil e não contêm secrets |
| `27f97cc` | VS Code, hook e instalador portátil | Manter; a configuração é útil nesta e em futuras máquinas |

Eles estão isolados e sincronizados em
`origin/docs/KAN-27-preserve-engineering-docs`, ainda sem merge em `main`.
Desfazê-los voltaria a deixar documentos e configurações somente na máquina
local, sem corrigir nenhum problema técnico.

Antes do merge, a branch precisa de uma revisão documental porque o guia de
autenticação ainda contém evidências anteriores à PR web `#70`:

- a tela `/sign-in` já deixou de ser placeholder em KAN-85;
- a introdução ainda cita uma migração de hosting que foi cancelada em KAN-157;
- KAN-88, KAN-89 e KAN-91 precisam aparecer na ordem do gate local;
- a fotografia de branches e commits deve ser atualizada para 2026-08-23.

## Estado confirmado em 2026-08-23

### Git e GitHub

| Repositório | Estado | Próxima correção |
| --- | --- | --- |
| `sandicts-docs` | branch de preservação sincronizada, com este plano e a revisão do guia ainda locais | validar o diff, revisar, abrir PR e integrar |
| `nodejs-sandicts-api` | `developer` alinhada a `origin/developer` em `405ec06` | manter limpa até a próxima issue da API |
| `reactjs-sandicts-web` | `developer` alinhada a `origin/developer` em `f12155d` | corrigir os gates encontrados na validação da KAN-85 em entrega própria |

Não há PRs abertas na API ou no web. Em documentação existe somente a PR draft
`#11` da KAN-54, que é outra frente e não deve ser misturada neste plano.

### Jira

| Issue | Estado atual | Tratamento neste plano |
| --- | --- | --- |
| KAN-85 | In Review | merge validado visualmente; manter em revisão até corrigir formatação e sincronizar o cliente OpenAPI |
| KAN-150 | A fazer | executar primeiro: Google Cloud nonprod e clientes Local/Beta |
| KAN-86 | A fazer | Google Sign-In explícito |
| KAN-87 | A fazer | Google One Tap após KAN-86 |
| KAN-105 | A fazer | magic link funcional com Mailpit |
| KAN-88 | A fazer | proteger rotas depois da sessão funcional |
| KAN-89 | A fazer | sign-out e limpeza de cache |
| KAN-158 | A fazer | restringir cadastro a convidados antes dos E2E finais |
| KAN-91 | A fazer | E2E de sessão expirada |
| KAN-90 | A fazer | E2E do happy path web |
| KAN-106 | A fazer | E2E do magic link via Mailpit |
| KAN-156 | A fazer | verificar domínio e chave Sending-only do Resend |
| KAN-27 | Em Progresso | configurar e provar o Beta depois de todos os gates |
| KAN-28 | A fazer | adiada; não criar Produção nesta fase |
| KAN-157 | Canceled | nenhuma execução |

Em 2026-08-23, as 15 issues que estavam indevidamente em `Em Progresso` foram
devolvidas para `A fazer`. KAN-27 é agora a única issue em progresso; KAN-85
permanece separadamente em revisão. O fluxo
`Backlog -> Next Up -> In Progress -> In Review -> Done` foi publicado no
quadro: o Backlog nativo foi habilitado, `Next Up` recebeu limite 5,
`In Progress` limite 1 e `In Review` limite 2. O status `Canceled` foi preservado
e mapeado à coluna final `Done`; a coluna vazia `CANCELED` foi removida.

## Regras de execução

1. Trabalhar em uma issue por vez.
2. Cada unidade deve caber em até 12 horas de planejamento Jira; dividir antes
   de implementar se a evidência indicar escopo maior.
3. Criar branch a partir de `origin/developer` atualizado.
4. Não misturar duas KANs no mesmo commit ou PR.
5. Executar validações locais antes de push.
6. Abrir PR para `developer`, aguardar CI e revisar o diff.
7. Integrar, confirmar o commit no remoto e somente então concluir a issue.
8. Nunca colocar Client Secret, API key, token, lista de convidados ou URLs de
   banco em Git, Jira, docs, logs ou chat.
9. Não iniciar Produção, plano pago ou add-on durante este plano.

## Ordem de implementação

| Ordem | Unidade | Resultado necessário |
| ---: | --- | --- |
| 0 | Higiene Git/Jira/docs | branches locais alinhadas, KAN-85 concluída e plano documental integrado |
| 1 | KAN-150 | clientes Google Web separados para Local e Beta |
| 2 | KAN-86 | botão Google explícito funcional localmente |
| 3 | KAN-87 | One Tap controlado por flag e com fallback |
| 4 | KAN-105 | solicitação e consumo de magic link via Mailpit |
| 5 | KAN-88 | rotas protegidas e retorno pós-login |
| 6 | KAN-89 | sign-out consistente no backend e no cache web |
| 7 | KAN-158 | novos usuários limitados aos convidados |
| 8 | KAN-91 | sessão expirada validada em E2E |
| 9 | KAN-90 | happy path web de autenticação validado |
| 10 | KAN-106 | magic link E2E validado pela API do Mailpit |
| 11 | KAN-156 | Resend verificado e credencial mínima criada |
| 12 | Gate técnico conjunto | API e web aprovados sem secrets de Beta locais |
| 13 | KAN-27 | Beta implantado, validado, recuperável e fechado |

## Passo 0 — Higiene antes do próximo código

### 0.1 Alinhar branches locais

Executar somente com os worktrees limpos.

Na API:

```powershell
git fetch --all --prune
git switch developer
git pull --ff-only
git status -sb
```

No web:

```powershell
git fetch --all --prune
git switch developer
git pull --ff-only
git status -sb
```

Não apagar imediatamente as branches locais antigas. Os bundles já preservam
o histórico, mas a limpeza pode ocorrer somente depois de todos os merges serem
confirmados.

### 0.2 Revisar e integrar a documentação atual

**Arquivos:**

- `sandicts-docs/docs/engineering/auth-local-preview-production-rollout-plan.md` — editar as evidências antigas;
- `sandicts-docs/docs/engineering/sandicts-current-work-completion-plan-2026-08-23.md` — revisar este plano;
- `sandicts-docs/tooling/workspace/install-workspace-config.ps1` — manter como fonte portátil já testada.

Depois da revisão:

```powershell
git diff --check
git status -sb
```

Criar um commit documental separado, publicar a branch, abrir PR para `main` e
integrar somente após revisão. A PR draft `#11` da KAN-54 permanece separada.

### 0.3 Fechar KAN-85 com evidência

A PR web `#70` foi mergeada em `developer` no commit `f12155d`, com CI verde.
O checkout foi atualizado e os comandos abaixo foram executados em 2026-08-23:

```powershell
npm ci
npm run quality
npm run test:ci
npm run api:check
npm run build
```

Resultado observado:

- lint, typecheck, checks visuais, 198 testes e build passaram;
- `/sign-in` foi validada em desktop e mobile, inclusive nos estados de loading
  e falha controlada sem a API local;
- `format:check` falhou em 13 arquivos da entrega;
- `api:check` detectou o cliente gerado sem os novos contratos de health da API
  atualizada;
- KAN-85 permanece `In Review` até esses dois gates serem corrigidos e
  reintegrados. O skeleton Google continua esperado e pertence à KAN-86.

### 0.4 Corrigir estados Jira

Normalização aplicada:

- KAN-27 foi mantida como a única issue `Em Progresso`;
- KAN-37, KAN-86, KAN-87, KAN-88, KAN-89, KAN-90, KAN-91, KAN-98, KAN-100,
  KAN-101, KAN-105, KAN-106, KAN-139, KAN-140 e KAN-141 foram devolvidas para
  `A fazer`;
- KAN-85 permanece `In Review` pelos gates documentados acima;
- KAN-150 só deve ir para `In Progress` quando KAN-27 deixar de representar a
  unidade ativa ou quando a política da tarefa agregadora for revisada;
- KAN-28 e KAN-157 não foram transicionadas.

Configuração do quadro publicada:

- Backlog nativo habilitado e adicionado à navegação do projeto;
- colunas operacionais `Next Up`, `In Progress`, `In Review` e `Done`;
- limites máximos de 5, 1 e 2 itens, respectivamente, nas três primeiras
  colunas;
- status `Canceled` mantido semanticamente e mapeado à coluna `Done`;
- coluna vazia `CANCELED` removida após confirmação do usuário.

## Passo 1 — KAN-150: Google Cloud nonprod

**Ação:** configuração externa, sem código.

1. Proteger a conta Google proprietária com recuperação e MFA.
2. Criar ou confirmar o projeto `sandicts-auth-nonprod`.
3. Configurar consentimento em modo Testing.
4. Criar um cliente Web Local limitado a `http://localhost:3001`.
5. Criar um cliente Web Beta limitado a `https://preview.sandicts.com.br`.
6. Cadastrar somente os testadores autorizados.
7. Guardar os Client IDs nos destinos corretos; não registrar valores no Jira.
8. Não criar cliente Production.

**Validação:** os dois Client IDs são diferentes e cada um aceita somente suas
origens autorizadas.

## Passo 2 — KAN-86: Google Sign-In explícito

**Branch sugerida:** `feature/KAN-86-google-sign-in`

**Arquivos evidenciados:**

- `reactjs-sandicts-web/src/features/auth/sign-in/components/google-sign-in-host.tsx` — substituir o skeleton pelo host GIS real;
- `reactjs-sandicts-web/src/features/auth/hooks/use-google-sign-in.ts` — reutilizar e ajustar a mutação existente;
- `reactjs-sandicts-web/src/features/auth/sign-in/components/auth-method-stack.tsx` — manter composição dos métodos;
- `reactjs-sandicts-web/src/lib/env/public-env.ts` — usar o contrato existente;
- `reactjs-sandicts-web/src/i18n/messages/pt-BR.json` — mensagens acessíveis de loading/erro;
- testes próximos aos componentes e hook alterados.

**Comportamentos obrigatórios:** carregar GIS uma vez, renderizar o botão
oficial, enviar somente a credential ao backend, hidratar a sessão, respeitar
`returnTo` seguro e não persistir access token em storage.

**Validação focada:** testes do componente/hook, login manual Local, audience
inválida rejeitada e cookie de refresh `HttpOnly`.

## Passo 3 — KAN-87: Google One Tap

**Branch sugerida:** `feature/KAN-87-google-one-tap`

**Arquivos prováveis, a confirmar ao iniciar a issue:**

- novo componente/hook sob `reactjs-sandicts-web/src/features/auth/`;
- `reactjs-sandicts-web/src/app/providers.tsx` para ciclo de vida global;
- `reactjs-sandicts-web/src/lib/env/public-env.ts` para a flag existente;
- testes de supressão, cancelamento, loading e fallback.

One Tap começa desabilitado localmente. O fallback obrigatório é o botão da
KAN-86. A validação real final acontece no HTTPS do Beta.

## Passo 4 — KAN-105: magic link local

**Branch sugerida:** `feature/KAN-105-magic-link-sign-in`

**Arquivos esperados:**

- componentes sob `reactjs-sandicts-web/src/features/auth/sign-in/`;
- nova rota `reactjs-sandicts-web/src/app/(public)/sign-in/magic-link/page.tsx`;
- hooks de solicitar e consumir link sob `src/features/auth/hooks/`;
- cliente gerado existente em `src/lib/api/generated/sandicts-api/auth/`;
- mensagens em `src/i18n/messages/pt-BR.json`;
- testes de solicitação, consumo, expiração, reutilização e rate limit.

**Regra de segurança:** a resposta de solicitação é genérica e nunca revela se
o email existe. O token é removido do histórico do navegador e nunca lido de
logs pelos testes.

**Validação:** PostgreSQL + Mailpit locais, resposta `202`, consumo por POST,
sessão criada, reutilização rejeitada e link anterior substituído.

## Passo 5 — KAN-88: rotas protegidas

**Branch sugerida:** `feature/KAN-88-protected-routes`

**Arquivos evidenciados:**

- `reactjs-sandicts-web/src/app/(player)/app/layout.tsx`;
- `reactjs-sandicts-web/src/app/(organization)/organizations/[organizationSlug]/layout.tsx`;
- `reactjs-sandicts-web/src/lib/auth/auth-session-provider.tsx`;
- `reactjs-sandicts-web/src/lib/routes/safe-return-to.ts`;
- testes dos layouts/guards e do retorno seguro.

Tratar separadamente hidratação, signed-out, sessão expirada, forbidden e
usuário autenticado. Nenhum conteúdo protegido pode piscar antes do gate.

## Passo 6 — KAN-89: sign-out

**Branch sugerida:** `feature/KAN-89-sign-out-flow`

**Arquivos evidenciados:**

- `reactjs-sandicts-web/src/components/shared/app-shell/chrome/authenticated-topbar.tsx`;
- novo hook de sign-out sob `src/features/auth/hooks/`;
- `src/features/auth/hooks/auth-session-mutation-handlers.ts`;
- `src/lib/auth/auth-session-cache.ts` e `auth-session-store.ts`;
- runtime API sob `src/lib/api/runtime/` e testes correspondentes.

O backend deve revogar a sessão, o web deve limpar dados protegidos e a falha
não pode manter uma aparência enganosa de autenticação.

## Passo 7 — KAN-158: somente convidados

**Branches sugeridas:**

- API: `feature/KAN-158-invite-only-beta`;
- web somente se houver mensagem/estado necessário no mesmo contrato.

**Decisão arquitetural recomendada:** fonte de convidados no PostgreSQL, não em
Git, variável pública ou lista de emails no deploy. A implementação detalhada
precisa ser confirmada ao iniciar a issue.

**Áreas afetadas na API:**

- `prisma/models/auth.prisma` e migration dedicada;
- nova porta/repositório para convites sob `src/modules/auth/`;
- use cases `google-sign-in`, `request-magic-link` e/ou `consume-magic-link`;
- respostas genéricas contra enumeração;
- testes para convidado, não convidado, removido e revogação;
- procedimento operacional versionado sem emails reais.

Nenhuma conta é criada antes da elegibilidade. Remover um convite deve impedir
novas sessões conforme a regra aprovada, sem registrar email completo em logs.

## Passos 8 a 10 — E2E finais

### KAN-91 — sessão expirada

**Branch:** `test/KAN-91-expired-session-e2e`

Criar teste Playwright para pelo menos uma rota de jogador e uma rota de
organização. Confirmar redirect/mensagem, limpeza de cache e ausência de dados
protegidos após expiração.

### KAN-90 — happy path web

**Branch:** `test/KAN-90-auth-happy-path`

Cobrir shell, sessão, retorno seguro, refresh e sign-out. Google real fica no
checklist manual; a automação usa boundary controlado, nunca senha de usuário.

### KAN-106 — magic link

**Branch:** `test/KAN-106-magic-link-e2e`

Criar helper Playwright para consultar a API do Mailpit e extrair o link da
mensagem, sem procurar token em logs. Cobrir uso único, expiração e usuário não
convidado.

## Passo 11 — KAN-156: Resend

**Ação:** configuração externa, sem valor secreto em código.

1. Criar ou confirmar conta sob controle do Sandicts.
2. Adicionar `mail.sandicts.com.br`.
3. Conferir registros DNS existentes antes de criar SPF/DKIM na Hostinger.
4. Aguardar domínio `Verified`.
5. Criar chave `sandicts-preview` com Sending access e restrição ao domínio.
6. Guardar a chave diretamente no destino do Beta.
7. Enviar teste controlado pelo provedor.

Não configurar Resend no ambiente Local; Local continua com Mailpit.

## Passo 12 — Gate técnico conjunto

Na API:

```powershell
npm ci
npm run lint:ci
npm run typecheck
npm run test:ci
npm run openapi:check
npm run build
npm audit --audit-level=moderate
docker build -t sandicts-api:local-gate .
```

No web:

```powershell
npm ci
npm run quality
npm run test:ci
npm run api:check
npm run build
npm run test:e2e
```

Além dos comandos, validar manualmente Google, magic link, refresh, sign-out,
rotas protegidas e bloqueio de não convidados. Nenhum secret de Beta deve estar
presente no ambiente local usado pelo gate.

## Passo 13 — KAN-27: concluir o Beta

Somente depois de todos os gates:

1. Criar o role Neon `sandicts_preview_app` com privilégios mínimos.
2. Usar URL pooled desse role somente no runtime Render.
3. Manter URL direct owner somente no GitHub Environment `preview` para migrations.
4. Completar secrets e variables Render/GitHub sem expor valores.
5. Confirmar Render em `staging`, Docker, Auto-Deploy Off e `/health/ready`.
6. Configurar `api.preview.sandicts.com.br` e TLS sem remover o domínio Render antes da validação.
7. Confirmar Vercel Hobby sem Pro/add-ons, CD próprio e domínio estável.
8. Abrir PR de promoção `developer -> staging` em cada repositório necessário.
9. Validar que migrations e deploys usam exatamente o SHA promovido.
10. Executar readiness, liveness, smoke e checklist de autenticação HTTPS.
11. Confirmar CORS, cookies, logs seguros e bloqueio de não convidados.
12. Ensaiar dump/restore em banco temporário e rollback da aplicação para SHA saudável.
13. Documentar aviso de Beta, privacidade e exclusão manual de dados.
14. Concluir KAN-27 somente depois de todas as evidências.

KAN-28 permanece `A fazer` e explicitamente adiada. Concluir KAN-27 não autoriza
criar Produção.

## Critério para voltar ao plano de migração

A mudança de máquina só volta a ser preparada quando:

- KAN-85, KAN-86, KAN-87, KAN-88, KAN-89, KAN-90, KAN-91, KAN-105,
  KAN-106, KAN-150, KAN-156, KAN-158 e KAN-27 estiverem concluídas;
- não houver PRs abertas da frente atual;
- `developer` e `staging` estiverem no estado esperado e todos os worktrees limpos;
- a branch documental deste plano estiver integrada em `main`;
- o Beta tiver evidência de deploy, autenticação, backup/restore e rollback;
- o pacote de transferência for recriado a partir do estado final, sem reutilizar
  como definitivo o snapshot de 2026-08-21.

## Rollback

- Código: reverter pelo fluxo de PR; não reescrever branches compartilhadas.
- Deploy: reimplantar o último SHA saudável; não usar `Deploy latest commit`.
- Banco: corrigir migrations para frente; nunca reverter schema de forma destrutiva.
- Secrets: rotacionar no provedor e atualizar somente o cofre de destino.
- Provedor externo indisponível ou cota Free esgotada: pausar o Beta, sem upgrade automático.

## Evidências e decisões ainda necessárias

- Aprovar a fonte PostgreSQL recomendada para convites ao iniciar KAN-158.
- Executar interativamente Google Cloud, Hostinger, Resend, Neon, Render e Vercel quando cada gate chegar.
- Revisar e integrar a branch documental antes de tratá-la como fonte canônica em `main`.
- Decidir se o pacote privado de transferência de 2026-08-21 deve ser apagado ou mantido offline; ele contém secrets em texto simples e ficará obsoleto após novas mudanças.
