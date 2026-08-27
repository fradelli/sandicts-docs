---
title: Plano zero-cost de autenticação local e Beta fechado
doc-type: implementation-plan
status: in-progress
last-reviewed: 2026-08-27
owners:
  - engineering
related-jira:
  - KAN-27
  - KAN-28
  - KAN-37
  - KAN-85
  - KAN-86
  - KAN-87
  - KAN-88
  - KAN-89
  - KAN-90
  - KAN-91
  - KAN-105
  - KAN-106
  - KAN-150
  - KAN-156
  - KAN-157
  - KAN-158
---

# Plano zero-cost de autenticação local e Beta fechado

## Status do guia

Planejamento revisado para custo obrigatório zero. A KAN-85 entregou o shell de
sign-in e sessão no frontend; a execução restante está bloqueada pelos cadastros
externos, pelas integrações de autenticação, pelos guards e pelos gates descritos
neste documento. O frontend permanece temporariamente na Vercel Hobby; a
migração de hosting foi removida do caminho crítico. Nenhum secret deve ser
copiado para este arquivo, Jira, Git, logs ou chat.

Atualização de handoff em 2026-08-27: KAN-150, KAN-86 e KAN-87 foram
concluídas; a KAN-105 está em revisão na PR web `#74`. A próxima unidade de
implementação prevista é KAN-88. Para a fotografia operacional usada na troca
de máquina, consultar `machine-transfer-handoff-2026-08-27.md`.

## Decisão executiva

A ordem aprovada é:

```text
Google não produtivo
  -> Google Sign-In local
  -> conta, domínio e chaves Resend
  -> magic link local com Mailpit
  -> suíte local completa
  -> restringir cadastro a testadores convidados
  -> manter frontend na Vercel Hobby com CD próprio
  -> Beta: Vercel + Render + Neon + Resend
  -> validar a ideia com usuários convidados
  -> decidir depois se Produção é necessária
```

Durante a validação existirão somente **Local + um ambiente Beta fechado**. Os
nomes técnicos `preview` e `staging` serão mantidos para evitar retrabalho, mas
esse ambiente será o único ambiente remoto usado por testadores. KAN-28 fica
adiada: não serão criados agora um segundo Render, um segundo Neon, outro
projeto Google ou credenciais de Produção.

Nenhum plano pago, add-on ou recarga automática será habilitado. Se uma cota
gratuita terminar, o Beta pausa até a renovação; isso é uma restrição aceita,
não um gatilho automático de upgrade.

Não é tecnicamente necessário criar uma nova conta Google pessoal. Uma conta
Google existente pode criar os projetos no Google Cloud. Para reduzir risco de
perda de acesso, o projeto deve ficar sob uma identidade controlada pelo
Sandicts, com recuperação configurada; um segundo proprietário pode ser
adicionado quando houver outra pessoa responsável pelo produto.

Para OAuth, será usado agora somente `sandicts-auth-nonprod`, em modo Testing,
para Local e Beta. Dentro dele, Local e Beta usam clientes OAuth Web
separados. Isso limita origens autorizadas e evita que um erro de configuração
local afete o Beta. Projeto e cliente Production ficam adiados com KAN-28.

Resend não substitui Mailpit no computador do desenvolvedor. O backend já
impõe esta regra:

- `APP_ENV=local` usa SMTP/Mailpit;
- `APP_ENV=staging` ou `production` usa Resend.

A conta e o domínio Resend podem ser verificados antes do deploy, e um envio
controlado pode ser feito pelo provedor. O primeiro teste completo do adapter
Resend dentro da aplicação acontece no Beta.

### Limites gratuitos aceitos

| Serviço | Limite/restrição relevante em 2026-08-17 | Resposta operacional |
| --- | --- | --- |
| Render Free | 750 horas mensais por workspace, spin-down por inatividade, filesystem efêmero e sem SLA | Manter um único Web Service; aceitar cold start e pausa |
| Neon Free | 100 CU-h/mês e 0,5 GB por projeto; restore dentro da janela publicada do plano | Monitorar uso e manter `pg_dump` criptografado durante testes ativos |
| Resend Free | 3.000 emails/mês e 100/dia | Limitar convites; pausar magic link ao atingir a cota |
| Vercel Hobby | Custo zero, sujeito a Fair Use e aos termos pessoais/não comerciais publicados | Manter Beta fechado, não monetizado, sem Pro ou add-ons; reavaliar antes de operação comercial pública |

Por decisão do usuário, o Vercel Hobby existente continuará como destino
temporário do frontend. O Beta será fechado, não monetizado e não habilitará
Pro, add-ons ou cobrança. Os termos publicados do Hobby o limitam a uso pessoal
e não comercial; por isso essa decisão deve ser reavaliada antes de transformar
o piloto privado em operação comercial pública. Netlify, Cloudflare ou um VPS
próprio continuam como opções futuras, sem bloquear KAN-27.

## Estado real encontrado em 2026-08-23

### Backend

Já existe:

- endpoint e verificação server-side de Google ID token;
- `AUTH_GOOGLE_CLIENT_ID` validado por ambiente;
- gateway Resend atrás da porta `EMAIL_GATEWAY`;
- gateway SMTP compatível com Mailpit;
- construção do magic link por `WEB_APP_BASE_URL`;
- validação tipada que exige Resend em staging/production;
- Docker Compose com PostgreSQL 18 e Mailpit;
- pipeline Preview, migrations, deploy por SHA, health checks e rollback;
- Dockerfile na branch `developer`.

Não é esperada mudança de arquitetura no backend para iniciar a configuração.
Podem surgir correções pequenas durante o teste integrado, mas não deve ser
criada uma segunda implementação de Google ou email.

### Frontend

Já existe:

- contrato de variáveis públicas;
- cliente gerado da API de autenticação;
- hook inicial de Google Sign-In;
- shell funcional de `/sign-in`, hidratação e estados de sessão entregues pela
  KAN-85;
- store de sessão, refresh e retorno seguro;
- protótipos de Google e magic link.

Ainda falta:

- integrar o botão oficial Google;
- integrar One Tap e suas regras de supressão/fallback;
- implementar as telas funcionais de solicitação e consumo de magic link;
- proteger rotas autenticadas e implementar sign-out consistente;
- criar os E2E funcionais de autenticação.

Portanto, credenciais válidas não tornam o login utilizável sozinhas.

Não haverá migração de hosting antes do Beta. O frontend permanece na Vercel,
usando os workflows e Vercel CLI já definidos. Uma mudança futura deve comparar
Netlify, Cloudflare e self-hosting em VPS, incluindo o custo operacional de
backups, TLS, monitoramento e atualizações.

### Infraestrutura

Já existe:

- Vercel Preview em `https://preview.sandicts.com.br`, mantida como hosting
  temporário do Beta;
- Neon Preview `sandicts-api-preview`, PostgreSQL 18, banco
  `sandicts_preview`;
- GitHub Environment `preview` restrito a `staging`;
- secret `PREVIEW_DATABASE_DIRECT_URL`;
- Render Web Service `sandicts-api-preview`.

O primeiro deploy Render falhou corretamente porque a branch `staging` ainda
estava no SHA antigo `d90232d`, que não continha Dockerfile. Não se corrige esse
commit e não se faz deploy manual dele. A próxima tentativa ocorre somente após
os gates locais e a promoção de `developer` para `staging`.

O DNS autoritativo atual de `sandicts.com.br` está nos nameservers da Hostinger
(`lunar.dns-parking.com` e `solar.dns-parking.com`). Cloudflare não faz parte
deste plano enquanto os nameservers não forem alterados por uma decisão
separada.

O Beta poderá guardar somente os dados pessoais mínimos de testadores
convidados necessários a conta e sessão. Não aceitará pagamentos, documentos,
dados de saúde ou outros dados sensíveis. Antes da abertura, KAN-158 deve
impedir que Google ou magic link criem contas para emails não convidados e
deve existir aviso de privacidade e procedimento de exclusão.

## Mapa Jira sem duplicação

| Ordem | Jira | Resultado esperado |
| --- | --- | --- |
| 0 | KAN-85 | Shell de sign-in e sessão validado e concluído |
| 1 | KAN-150 | Google Cloud nonprod, consentimento e clientes Local/Beta |
| 2 | KAN-86 | Botão Google funcional no frontend |
| 3 | KAN-87 | One Tap funcional com fallback |
| 4 | KAN-105 | Magic link funcional no frontend |
| 5 | KAN-88 | Rotas protegidas e retorno pós-login |
| 6 | KAN-89 | Sign-out e limpeza consistente da sessão |
| 7 | KAN-158 | Cadastro restrito a testadores convidados |
| 8 | KAN-91 | Sessão expirada validada em E2E |
| 9 | KAN-90 | Happy path web de autenticação validado |
| 10 | KAN-106 | Magic link E2E com Mailpit validado |
| 11 | KAN-156 | Conta, domínio e credencial Resend Free do Beta |
| Cancelada | KAN-157 | Migração Netlify removida do caminho crítico do Beta |
| 12 | KAN-27 | Beta completo em Vercel + Render + Neon |
| Adiada | KAN-28 | Produção somente após validação e nova decisão |

KAN-156 foi criada porque KAN-102 registrava somente a decisão do provedor.
KAN-157 foi cancelada depois da decisão de manter a Vercel nesta fase. KAN-158
continua necessária porque o modo Testing do Google não protege o fluxo de
magic link. Não foram duplicadas as tarefas existentes de Google, interface ou
E2E.

## Gate 0 — Segurança e propriedade das contas

Antes de abrir qualquer painel:

1. Confirmar qual conta Google será proprietária dos projetos Sandicts.
2. Ativar autenticação em dois fatores nessa conta.
3. Confirmar email e telefone de recuperação.
4. Usar um gerenciador de senhas para credenciais e recovery codes.
5. Nunca reutilizar o JWT secret entre Local, Preview e Produção.
6. Nunca salvar API keys ou connection strings em bloco de notas, Jira ou Git.
7. Manter Client IDs Google em variáveis públicas somente porque Client ID não
   é secret; nenhum OAuth client secret será usado no fluxo GIS atual.

Critério de saída:

- a conta proprietária é recuperável e os projetos podem ser administrados sem
  credenciais compartilhadas informalmente.

## Fase 1 — Google Cloud não produtivo (KAN-150)

### 1.1 Criar o projeto

1. Entrar em `https://console.cloud.google.com/`.
2. Abrir o seletor de projetos e escolher **New Project**.
3. Nome recomendado: `sandicts-auth-nonprod`.
4. Não habilitar billing apenas para Google Identity Services, salvo se o
   próprio console passar a exigir para algum recurso adicional.
5. Registrar apenas o nome e o Project ID no inventário operacional; eles não
   são secrets.

### 1.2 Configurar Google Auth Platform

1. Abrir **Google Auth Platform**.
2. Em **Branding**, configurar:
   - nome: `Sandicts`;
   - support email sob controle do projeto;
   - logo somente quando o ativo final estiver aprovado;
   - homepage: `https://sandicts.com.br`;
   - privacy policy e terms em URLs públicas do mesmo domínio quando exigidas;
   - developer contact email.
3. Em **Audience**:
   - tipo `External`;
   - publishing status `Testing`;
   - adicionar somente as contas Google que participarão dos testes.
4. Em **Data Access**:
   - manter apenas `openid`, `email` e `profile`;
   - não solicitar Gmail, Drive ou outros scopes.
5. Em **Authorized domains**, adicionar `sandicts.com.br` quando o console
   solicitar.
6. Se o Google exigir comprovação, verificar a propriedade do domínio no
   Search Console usando a mesma conta proprietária/editor do projeto.

### 1.3 Criar o cliente Local

1. Abrir **Clients** e escolher **Create client**.
2. Tipo: **Web application**.
3. Nome: `sandicts-web-local`.
4. Authorized JavaScript origins:
   - `http://localhost:3001`
5. Não adicionar `localhost:3000`: esse é o backend, não a página que executa
   o Google Identity Services.
6. Não adicionar redirect URI: o frontend atual recebe a credential por
   callback JavaScript e a envia a `POST /auth/google/sign-in`.
7. Guardar o Client ID; não guardar nem distribuir client secret.

### 1.4 Criar o cliente Preview

1. Criar outro cliente Web.
2. Nome: `sandicts-web-preview`.
3. Authorized JavaScript origins:
   - `https://preview.sandicts.com.br`
4. Não cadastrar URLs geradas `*.vercel.app`.
5. Não cadastrar `https://api.preview.sandicts.com.br` como JavaScript origin,
   porque GIS executa no frontend.
6. Guardar o Client ID de Preview separado do Client ID Local.

### 1.5 Não criar ainda o cliente Production

O projeto `sandicts-auth-production` e o cliente `sandicts-web-production` não
serão criados durante a validação. KAN-28 só reabre essa decisão depois que o
Beta produzir aprendizado suficiente e houver autorização explícita.

Critérios de saída:

- consentimento não produtivo em Testing;
- conta de teste cadastrada;
- cliente Local aceita somente `http://localhost:3001`;
- cliente Preview aceita somente `https://preview.sandicts.com.br`;
- nenhum secret foi criado no frontend ou documentado.

## Fase 2 — Implementar e testar Google localmente

### 2.1 Arquivos envolvidos

| Repositório/arquivo | Ação futura | Motivo |
| --- | --- | --- |
| `reactjs-sandicts-web/src/app/(public)/sign-in/page.tsx` | Reusar | Shell funcional entregue pela KAN-85 |
| `reactjs-sandicts-web/src/features/auth/hooks/use-google-sign-in.ts` | Reusar/ajustar | Enviar a credential ao endpoint existente |
| `reactjs-sandicts-web/src/features/auth/` | Criar componentes focados | Botão GIS, One Tap, magic link e estados |
| `reactjs-sandicts-web/src/lib/env/public-env.ts` | Manter contrato | Já valida Client ID e flag do One Tap |
| `reactjs-sandicts-web/.env.example` | Manter sem valor real | Documenta as variáveis sem expor configuração |
| `nodejs-sandicts-api/.env` | Configuração local não versionada | Mesmo Client ID Local usado pelo backend |
| `reactjs-sandicts-web/.env.local` | Configuração local não versionada | Client ID Local público e flags da interface |

### 2.2 Configuração local esperada

Backend, em arquivo local ignorado pelo Git:

```dotenv
NODE_ENV=development
APP_ENV=local
CORS_ALLOWED_ORIGINS=http://localhost:3001
AUTH_GOOGLE_CLIENT_ID=<CLIENT_ID_LOCAL>
AUTH_COOKIE_SECURE=false
AUTH_COOKIE_SAME_SITE=lax
EMAIL_DELIVERY_PROVIDER=smtp
SMTP_HOST=localhost
SMTP_PORT=1025
SMTP_SECURE=false
WEB_APP_BASE_URL=http://localhost:3001
```

Frontend, em `.env.local` ignorado pelo Git:

```dotenv
NEXT_PUBLIC_APP_ENV=local
NEXT_PUBLIC_API_BASE_URL=http://localhost:3000
NEXT_PUBLIC_AUTH_ENABLED=true
NEXT_PUBLIC_GOOGLE_CLIENT_ID=<MESMO_CLIENT_ID_LOCAL>
NEXT_PUBLIC_GOOGLE_ONE_TAP_ENABLED=false
WEB_ORIGIN=http://localhost:3001
SEO_INDEXING_ENABLED=false
```

O mesmo Client ID precisa estar nos dois lados porque o backend valida o claim
`aud` do ID token. O access token secret local continua sendo um valor somente
de desenvolvimento; ele não será promovido.

### 2.3 Ordem de implementação e teste

1. Concluir KAN-86 primeiro, com o botão explícito Google.
2. Executar PostgreSQL e Mailpit pelo Compose do backend.
3. Aplicar migrations locais.
4. Iniciar backend em `http://localhost:3000`.
5. Iniciar frontend em `http://localhost:3001`.
6. Entrar com uma conta listada como test user.
7. Confirmar:
   - token Google aceito pelo backend;
   - conta/identidade criada ou vinculada corretamente;
   - access token mantido somente em memória;
   - refresh cookie `HttpOnly`, `SameSite=Lax`, `Secure=false`, host-only;
   - refresh rotaciona a sessão;
   - logout revoga a sessão e remove o cookie;
   - audience inválida retorna erro sem criar sessão.
8. Implementar KAN-87 com One Tap inicialmente atrás de
   `NEXT_PUBLIC_GOOGLE_ONE_TAP_ENABLED=false`.
9. Validar comportamento de fallback/supressão por testes de componente.
10. Fazer a validação real final de One Tap em Preview HTTPS. O botão explícito
    é o gate local obrigatório; o prompt real do One Tap depende do contexto
    seguro e das regras do navegador/GIS.

Critério de saída:

- KAN-86 funcional localmente;
- regras KAN-87 cobertas por testes locais;
- nenhuma configuração de Beta/Produção foi usada no computador.

## Fase 3 — Provisionar Resend (KAN-156)

### 3.1 Criar a conta e o domínio

1. Criar a conta em `https://resend.com/` sob controle do Sandicts.
2. Abrir **Domains** e adicionar `mail.sandicts.com.br`.
3. Usar subdomínio dedicado para isolar reputação transacional e preservar o
   domínio raiz.
4. No painel DNS autoritativo atual da Hostinger, criar exatamente os registros
   SPF e DKIM fornecidos pelo Resend.
5. Antes de alterar SPF/DMARC, pesquisar registros existentes. Não criar dois
   SPF para o mesmo hostname e não substituir uma política DMARC existente.
6. DMARC é recomendado, mas deve ser adicionado em mudança separada e segura se
   já houver política organizacional. Para esta fase, SPF e DKIM verificados são
   o gate mínimo do Resend.
7. Voltar ao Resend e aguardar o status **Verified**.

Remetente recomendado:

```text
Sandicts <auth@mail.sandicts.com.br>
```

O endereço pode ser usado após a verificação do domínio; Resend não exige criar
uma caixa postal para cada `from`. Se respostas de usuário forem esperadas,
configurar um `reply-to` que realmente receba mensagens.

### 3.2 Criar somente a chave do Beta

Criar uma API key `sandicts-preview` com permissão **Sending access** e
restrição ao domínio verificado. O nome técnico Preview é mantido, mas a chave
atende ao único ambiente Beta.

Regras:

- copiar o valor uma única vez diretamente para o cofre de destino;
- a chave irá para o Render Beta;
- não usar Full access no runtime;
- rotacionar imediatamente qualquer chave exibida em chat, log ou arquivo;
- registrar no inventário apenas nome, ambiente, data de criação e responsável,
  nunca o valor.

### 3.3 Teste anterior ao deploy

1. Usar o recurso de test email do Resend para enviar ao email do proprietário.
2. Confirmar aceitação e entrega.
3. Confirmar que o `from` usa o domínio verificado.
4. Não modificar o backend Local para `resend`; isso violaria o contrato de
   ambiente já implementado.

Critério de saída:

- domínio Verified;
- envio controlado entregue;
- chave Beta Sending-only e restrita ao domínio;
- nenhum valor copiado para Jira, Git, docs ou chat.

## Fase 4 — Implementar e testar magic link localmente

### 4.1 Serviços locais

No repositório do backend:

```powershell
docker compose up -d postgres mailpit
npm ci
npm run prisma:migrate:deploy
npm run start:dev
```

Mailpit:

- SMTP: `localhost:1025`;
- interface: `http://localhost:8025`.

Em outro terminal, no frontend:

```powershell
npm ci
npm run dev
```

### 4.2 Implementação KAN-105

1. Compor o magic link dentro do shell funcional de `/sign-in` entregue pela KAN-85.
2. Implementar solicitação de magic link sem revelar se a conta existe.
3. Implementar estado de email enviado.
4. Criar/implementar `/sign-in/magic-link`.
5. Ler o token da URL, removê-lo do histórico e consumi-lo por `POST`.
6. Tratar link inválido, expirado, usado, substituído e rate limited.
7. Usar o mesmo store de sessão e o mesmo roteamento pós-login do Google.

### 4.3 Validação manual com Mailpit

1. Solicitar um link para um email de teste.
2. Confirmar resposta genérica `202`.
3. Abrir a mensagem somente no Mailpit.
4. Confirmar que token ou URL completa não aparecem nos logs do backend.
5. Abrir o link no navegador.
6. Confirmar consumo por `POST`, criação da sessão e rota pós-login.
7. Reutilizar o link e confirmar rejeição.
8. Solicitar outro link e confirmar que o anterior foi superseded.
9. Validar expiração e rate limit.

### 4.4 Automação KAN-90 e KAN-106

1. KAN-90 cobre o happy path web, sessão e roteamento.
2. KAN-106 consulta Mailpit por API/test helper; nunca lê token de logs.
3. Google real não deve ser automatizado com usuário/senha no Playwright.
   Testes automatizados usam boundary/mock controlado; a validação real fica em
   checklist manual Local/Preview.

Critério de saída:

- Google explícito local aprovado;
- magic link local aprovado com Mailpit;
- refresh/logout aprovados;
- KAN-90 e KAN-106 verdes;
- Resend ainda não é dependência do ambiente local.

## Fase 5 — Gate técnico antes do Beta

Executar nos dois repositórios, sem secrets de Preview:

### Backend

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

### Frontend

```powershell
npm ci
npm run quality
npm run test:ci
npm run api:check
npm run build
npm run test:e2e
```

O gate falha se:

- a interface `/sign-in` continuar como placeholder;
- Google e backend usarem Client IDs diferentes;
- One Tap estiver habilitado sem Client ID;
- magic link depender de Resend localmente;
- houver token/secret em log ou artefato;
- refresh/logout não funcionarem;
- Google ou magic link permitirem criar conta de usuário não convidado;
- contrato OpenAPI ou cliente gerado estiverem divergentes.

## Fase 6 — Beta zero-cost em Vercel, Render e Neon (KAN-27)

Os recursos continuam nomeados como Preview para preservar a configuração já
criada. Para usuários convidados, porém, este é o único ambiente Beta remoto.

### 6.1 Neon Preview

No projeto já criado:

1. Manter PostgreSQL 18 e database `sandicts_preview`.
2. Criar role `sandicts_preview_app` com senha exclusiva.
3. Conceder somente conexão, uso do schema, DML em tabelas e uso das sequences.
4. Definir default privileges do owner para que novas tabelas/sequences criadas
   por migrations também sejam acessíveis ao role de aplicação.
5. Não conceder ownership, criação de database, criação de role ou privilégios
   administrativos ao runtime.
6. Copiar a pooled URL do role de aplicação diretamente para `DATABASE_URL` no
   Render.
7. Manter a direct owner URL exclusivamente em
   `PREVIEW_DATABASE_DIRECT_URL` no GitHub Environment.
8. Nunca executar `pg_dump` ou operações administrativas pela pooled URL.

Privilégios esperados, adaptando owner/schema se necessário:

```sql
GRANT CONNECT ON DATABASE sandicts_preview TO sandicts_preview_app;
GRANT USAGE ON SCHEMA public TO sandicts_preview_app;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO sandicts_preview_app;
GRANT USAGE, SELECT, UPDATE ON ALL SEQUENCES IN SCHEMA public TO sandicts_preview_app;
ALTER DEFAULT PRIVILEGES FOR ROLE neondb_owner IN SCHEMA public
  GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO sandicts_preview_app;
ALTER DEFAULT PRIVILEGES FOR ROLE neondb_owner IN SCHEMA public
  GRANT USAGE, SELECT, UPDATE ON SEQUENCES TO sandicts_preview_app;
```

### 6.2 Render Preview

Confirmar:

| Campo | Valor |
| --- | --- |
| Service | `sandicts-api-preview` |
| Repository | `sandicts/nodejs-sandicts-api` |
| Branch | `staging` |
| Runtime | Docker |
| Dockerfile | `./Dockerfile` |
| Plan | Free |
| Region | Virginia |
| Auto-Deploy | Off |
| Health Check Path | `/health/ready` |

Variáveis/secrets do runtime:

| Nome | Regra Preview |
| --- | --- |
| `NODE_ENV` | `production` |
| `APP_ENV` | `staging` |
| `APP_HOST` | `0.0.0.0` |
| `DATABASE_URL` | pooled URL do role `sandicts_preview_app` |
| `CORS_ALLOWED_ORIGINS` | `https://preview.sandicts.com.br` |
| `WEB_APP_BASE_URL` | `https://preview.sandicts.com.br` |
| `AUTH_ACCESS_TOKEN_SECRET` | exclusivo, aleatório, mínimo 32 caracteres |
| `AUTH_GOOGLE_CLIENT_ID` | Client ID Preview |
| `AUTH_COOKIE_SECURE` | `true` |
| `AUTH_COOKIE_SAME_SITE` | `lax` quando os domínios customizados estiverem ativos |
| `EMAIL_DELIVERY_PROVIDER` | `resend` |
| `EMAIL_FROM_ADDRESS` | `auth@mail.sandicts.com.br` |
| `EMAIL_FROM_NAME` | `Sandicts` |
| `RESEND_API_KEY` | chave Preview Sending-only |
| `LOG_LEVEL` | `info` |
| `LOG_PRETTY` | `false` |

Não cadastrar `PREVIEW_DATABASE_DIRECT_URL` no Render.

### 6.3 GitHub Environment Preview

Secrets:

- `PREVIEW_DATABASE_DIRECT_URL` — já cadastrado;
- `RENDER_PREVIEW_DEPLOY_HOOK_URL`;
- `RENDER_API_KEY`.

Variables:

- `RENDER_PREVIEW_SERVICE_ID`;
- `PREVIEW_API_BASE_URL`, inicialmente `onrender.com` e depois
  `https://api.preview.sandicts.com.br`.

Manter deployment branch restrita a `staging`.

### 6.4 Domínio API Preview

1. Adicionar `api.preview.sandicts.com.br` em Render > Custom Domains.
2. Copiar o target exato mostrado pelo Render.
3. Criar o CNAME correspondente na zona Hostinger.
4. Aguardar verificação e TLS gerenciado.
5. Não remover o domínio `onrender.com` até health e custom domain passarem.
6. Atualizar a variável `PREVIEW_API_BASE_URL` e a configuração Vercel.

### 6.5 Vercel Hobby temporária

Antes de abrir o Beta:

1. Manter o projeto existente no plano Hobby.
2. Não habilitar Pro, add-ons ou qualquer cobrança.
3. Manter Git Integration desconectada; o GitHub Actions continua controlando
   o CD.
4. Fixar a versão do Vercel CLI usada pelo workflow.
5. Publicar somente o SHA aprovado de `staging` e manter
   `preview.sandicts.com.br` como domínio estável HTTPS.
6. Não cadastrar URLs geradas `*.vercel.app` em CORS ou no Google.
7. Manter o piloto fechado, não monetizado e restrito aos convidados.
8. Reavaliar os termos do Hobby antes de abertura comercial pública.

Configurar o contexto estável do Beta:

```dotenv
NEXT_PUBLIC_APP_ENV=preview
NEXT_PUBLIC_API_BASE_URL=https://api.preview.sandicts.com.br
NEXT_PUBLIC_AUTH_ENABLED=true
NEXT_PUBLIC_GOOGLE_CLIENT_ID=<CLIENT_ID_PREVIEW>
NEXT_PUBLIC_GOOGLE_ONE_TAP_ENABLED=true
WEB_ORIGIN=https://preview.sandicts.com.br
SEO_INDEXING_ENABLED=false
```

Os valores `NEXT_PUBLIC_*` são públicos e entram no bundle; mudar qualquer um
exige novo deploy. A Resend API key e URLs de banco nunca vão para Vercel.

O `VERCEL_TOKEN`, `VERCEL_ORG_ID` e `VERCEL_PROJECT_ID` ficam somente no GitHub
Environment `preview`. URLs efêmeras `*.vercel.app` não entram em CORS nem nas
Authorized JavaScript Origins do Google.

### 6.6 Primeiro deploy correto

```text
PR developer -> staging
  -> CI da PR
  -> merge de promoção
  -> full CI no SHA de staging
  -> prisma migrate deploy com direct owner URL
  -> deploy frontend pelo Vercel CLI com o SHA aprovado
  -> deploy hook Render com ref=<SHA exato>
  -> verificar SHA ativo
  -> GET /health/ready
  -> GET /health/live
  -> smoke tests
```

O Render permanece com Auto-Deploy Off. Não usar **Deploy latest commit**.

### 6.7 Checklist funcional do Beta

1. Aguardar eventual cold start.
2. Validar readiness e liveness.
3. Abrir `https://preview.sandicts.com.br/sign-in`.
4. Validar botão Google com test user.
5. Validar One Tap e fallback em navegador compatível.
6. Confirmar audience do Client ID Preview.
7. Solicitar magic link para testador convidado.
8. Confirmar entrega real via Resend.
9. Consumir link e validar sessão.
10. Validar refresh e logout.
11. Validar CORS rejeitando localhost, Production e `*.vercel.app`.
12. Confirmar logs sem tokens, cookies ou secrets.
13. Confirmar que nenhum dado foi criado fora de `sandicts_preview`.
14. Confirmar que Google e magic link não criam conta para não convidados.
15. Exibir aviso de Beta, privacidade e indisponibilidade possível.

### 6.8 Recuperação do Beta

1. Inserir dados sintéticos e, durante o piloto, somente dados mínimos dos
   testadores convidados.
2. Criar dump criptografado pela conexão direct.
3. Restaurar em database/branch temporária, nunca por cima do Preview ativo.
4. Executar smoke queries.
5. Descartar o recurso temporário após evidência.
6. Testar o rollback da aplicação para SHA saudável sem reverter migration.

Critério de saída:

- KAN-27 concluída como Beta zero-cost;
- autenticação real aprovada em HTTPS;
- deploy corresponde ao SHA promovido;
- backup/restore e rollback ensaiados;
- cadastro público bloqueado;
- Produção não foi criada.

## Fase 7 — Produção adiada (KAN-28)

### 7.1 Decisão atual

Não criar Produção durante a validação inicial. Local + Beta entregam o
aprendizado necessário sem duplicar compute, banco, secrets, OAuth, email e
trabalho operacional.

### 7.2 Condição para retomar

KAN-28 só será retomada quando:

1. o Beta estiver estável com testadores convidados;
2. os fluxos centrais tiverem sido usados e avaliados;
3. houver decisão explícita de continuar após a validação;
4. for escolhida uma estratégia compatível com custo zero ou, no futuro,
   existir orçamento expressamente autorizado.

### 7.3 Estratégias futuras possíveis

Na retomada, escolher uma das opções:

- promover o Beta existente para `sandicts.com.br` e
  `api.sandicts.com.br`, evitando duplicação;
- continuar nos provedores gratuitos enquanto os limites forem suficientes;
- migrar o frontend para Netlify ou Cloudflare; ou
- contratar um VPS e centralizar projetos com containers, proxy reverso, TLS,
  banco, backups e monitoramento administrados pelo Sandicts;
- criar ambiente separado somente se houver benefício operacional e franquia
  gratuita suficiente.

Limites, preços, termos, segurança e custo operacional devem ser revalidados na
data da decisão. Para hospedar aplicações, o recurso correto é um **VPS**
(servidor virtual privado), não uma VPN.

### 7.4 Regras permanentes

- GitHub Actions continua responsável por CI, migrations e deploy do SHA exato.
- Auto-deploy nativo do provedor permanece desligado.
- Secrets de um ambiente nunca são reutilizados em outro.
- Migrations usam direct owner URL; runtime usa pooled app role.
- Nenhuma cobrança automática é habilitada.
- Cota esgotada causa pausa controlada, não contratação.
- Dados pessoais continuam minimizados e sujeitos a exclusão.

### 7.5 Infraestrutura de referência

Se um ambiente separado for aprovado no futuro, a referência é:

| Campo | Valor |
| --- | --- |
| Service | `sandicts-api-production` |
| Branch | `master` |
| Runtime | Docker |
| Auto-Deploy | Off |
| Health Check | `/health/ready` |
| Domain | `api.sandicts.com.br` |

Ele também exigirá frontend, projeto/database/role Neon, projeto/cliente Google,
JWT, Resend e GitHub Environment exclusivos. Nada disso será provisionado agora.

### 7.6 Gate econômico

Nenhuma contratação paga é obrigatória para retomar KAN-28. Se os planos
gratuitos continuarem suficientes, Produção poderá operar neles aceitando cold
start, pausa por cota, janela curta de restore, ausência de SLA e possível
indisponibilidade. O usuário decide futuramente se esse risco ainda é aceitável.

### 7.7 Critério de saída desta fase

- KAN-28 permanece em `A fazer`;
- nenhum recurso de Produção foi criado;
- Beta pode abrir sem depender de KAN-28;
- a retomada requer nova decisão explícita.

## Matriz ativa durante a validação

| Item | Local | Beta fechado | Produção |
| --- | --- | --- | --- |
| Frontend | localhost:3001 | preview.sandicts.com.br na Vercel Hobby | adiada |
| API | localhost:3000 | api.preview.sandicts.com.br no Render Free | adiada |
| Banco | PostgreSQL 18 local | Neon Free `sandicts_preview` | adiada |
| Runtime DB | role local | pooled app role | adiada |
| Migrations | local/admin | direct owner via GitHub | adiada |
| Google | client Local | client Beta nonprod/Testing | adiada |
| Email app | Mailpit SMTP | Resend Free | adiada |
| Resend API key | nenhuma | Sending-only Beta | adiada |
| JWT secret | dev-only | exclusivo Beta | adiada |
| Deploy | manual local | `staging` via GitHub Actions | adiada |
| Dados | sintéticos | mínimo de convidados + sintéticos | nenhum ambiente criado |
| Custo obrigatório | zero | zero | zero enquanto adiada |

## Rollback geral

1. Falha local: corrigir na task branch; nenhum recurso remoto é afetado.
2. Falha Beta antes de migration: deploy não ocorre.
3. Falha Beta depois de migration: aplicar correção forward e reimplantar;
   usar SHA saudável apenas se o schema continuar compatível.
4. Falha de Google: desabilitar One Tap no frontend e manter botão/fallback
   somente se o Client ID e a sessão continuarem válidos; não usar credencial
   fictícia.
5. Falha de Resend: retornar indisponibilidade controlada; não registrar token
   nem trocar Beta para SMTP improvisado.
6. Falha ou esgotamento de cota: pausar o Beta e exibir indisponibilidade; não
   habilitar upgrade ou recarga automática.
7. Falha da Vercel: pausar o Beta, corrigir o CD e revalidar o último deployment
   saudável antes de reabrir aos convidados.
8. Comprometimento de secret: rotacionar no provedor, atualizar somente o cofre
   de destino e reimplantar; não registrar o valor antigo na evidência.

## Critérios globais de aceite

- [ ] Conta Google proprietária protegida e recuperável.
- [ ] Google nonprod configurado com clientes Local e Preview separados.
- [ ] Google Sign-In explícito funciona localmente.
- [ ] Frontend `/sign-in` não é mais placeholder.
- [ ] Magic link funciona localmente com Mailpit.
- [ ] KAN-90 e KAN-106 aprovadas.
- [ ] Resend verificado em `mail.sandicts.com.br`.
- [ ] Chave Resend Beta é Sending-only e restrita ao domínio.
- [ ] Frontend permanece no Vercel Hobby sem Pro/add-ons e usa CD próprio.
- [ ] KAN-158 impede cadastro de não convidados em Google e magic link.
- [ ] Beta usa app role pooled e migrations usam owner direct.
- [ ] Beta é implantado pelo SHA promovido e passa auth/health/smoke.
- [ ] Backup/restore, rollback e exclusão de dados Beta foram ensaiados.
- [ ] Somente dados mínimos de testadores convidados são coletados.
- [ ] Nenhum pagamento ou dado sensível entra na infraestrutura Free.
- [ ] Produção permanece sem recursos até nova decisão.
- [ ] Nenhuma cobrança automática ou add-on pago está habilitado.
- [ ] Nenhum secret aparece em Jira, Git, docs, logs ou variáveis públicas.

## Fontes oficiais consultadas

- [Google Identity Services: setup e Client ID](https://developers.google.com/identity/gsi/web/guides/get-google-api-clientid)
- [Google: audiência e publishing status](https://support.google.com/cloud/answer/15549945)
- [Google: validação server-side do ID token](https://developers.google.com/identity/gsi/web/guides/verify-google-id-token)
- [Google: requisitos de verificação](https://support.google.com/cloud/answer/13464321)
- [Resend: verificação de domínios](https://resend.com/docs/dashboard/domains/introduction)
- [Resend: permissões e restrição de API keys](https://resend.com/docs/dashboard/api-keys/introduction)
- [Resend: remetentes após domínio verificado](https://resend.com/docs/knowledge-base/how-do-I-create-an-email-address-or-sender-in-resend)
- [Resend: preços e Free tier](https://resend.com/pricing)
- [Resend: cotas e limites](https://resend.com/docs/knowledge-base/account-quotas-and-limits)
- [Render: deploy hooks e SHA específico](https://render.com/docs/deploy-hooks)
- [Render: deploys e Auto-Deploy Off](https://render.com/docs/deploys)
- [Render: health checks](https://render.com/docs/health-checks)
- [Render: limitações Free](https://render.com/docs/free)
- [Neon: pooled e direct connections](https://neon.com/docs/connect/connection-pooling)
- [Neon: databases e privilégios](https://neon.com/docs/manage/databases)
- [Neon: branching e recuperação](https://neon.com/docs/guides/branching-intro)
- [Neon: planos e janela de restore](https://neon.com/pricing)
- [Vercel Hobby](https://vercel.com/docs/plans/hobby)
- [Vercel Fair Use Guidelines](https://vercel.com/docs/limits/fair-use-guidelines)
- [Vercel Terms of Service](https://vercel.com/legal/terms)
- [Netlify pricing](https://www.netlify.com/pricing/)
- [Netlify Free para projetos comerciais](https://www.netlify.com/blog/introducing-netlify-free-plan/)
- [Netlify: suporte ao Next.js](https://docs.netlify.com/build/frameworks/framework-setup-guides/nextjs/overview/)
- [Netlify CLI: criação e deploys](https://docs.netlify.com/deploy/create-deploys/)
