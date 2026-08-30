---
title: Especificacao Funcional do MVP Sandicts
doc-type: product-functional-spec
role: working-draft
priority: high
canonical: docs/product/sandicts-mvp-functional-spec.md
related:
  - docs/product/sandicts-mvp-scope.md
  - docs/product/sandicts-product-context.md
  - docs/product/sandicts-open-match-model.md
  - docs/product/sandicts-open-match-implementation-roadmap.md
  - docs/product/sandicts-academy-plan-model.md
  - docs/business-rules/sandicts-business-rules.md
  - fradelli/reactjs-sandicts-web:docs/frontend/sandicts-frontend-tech-decisions.md
  - fradelli/reactjs-sandicts-web:docs/frontend/sandicts-mvp-delivery-roadmap.md
  - fradelli/reactjs-sandicts-web:docs/frontend/sandicts-frontend-planning.md
  - fradelli/reactjs-sandicts-web:docs/frontend/discovery/sandicts-mvp-frontend-roadmap-draft.md
scope: product, mvp, frontend, backend, figma, jira, api-contracts
read-when:
  - definir fluxos de usuario do MVP Sandicts
  - criar telas do MVP no Figma
  - criar issues de roadmap no Jira para modulos do MVP
  - desenhar contratos de API para modulos do MVP
  - planejar fatias verticais de backend e frontend
do-not-read-when:
  - implementar refatoracao tecnica somente de backend
  - alterar apenas CI, logger ou infraestrutura de baixo nivel
---

# Especificacao Funcional do MVP Sandicts

## Proposito

Este documento e a especificacao funcional de trabalho para o MVP do Sandicts.

Use este documento como ponte entre:

- escopo de produto
- regras de negocio
- telas no Figma
- roadmap no Jira
- contratos de API do backend
- implementacao do frontend
- validacao integrada

Este documento e mais detalhado do que
`docs/product/sandicts-mvp-scope.md`, mas ainda e um rascunho de trabalho.
Quando uma regra nao estiver decidida, mantenha como decisao aberta em vez de
transformar uma suposicao em regra de produto.

## Ordem Das Fontes De Verdade

Quando houver conflito entre documentos, preferir:

1. `docs/product/sandicts-mvp-scope.md`
2. `docs/business-rules/sandicts-business-rules.md`
3. este documento
4. drafts de descoberta do frontend
5. comportamento legado do `sports-rats-web`

O frontend legado serve como inspiracao. Ele nao e fonte de verdade de produto.

## Objetivo Do MVP

O MVP deve provar que o Sandicts funciona como marketplace e comunidade leve
para esportes de areia.

O MVP deve provar que:

- jogadores conseguem acessar o Sandicts com baixo atrito
- jogadores conseguem completar um perfil esportivo basico
- Organizations conseguem se cadastrar e expor quadras
- Organizations conseguem publicar disponibilidade
- jogadores conseguem descobrir quadras disponiveis
- jogadores conseguem solicitar reservas
- Organizations conseguem confirmar e gerenciar reservas
- Academies conseguem configurar planos e controlar acessos manualmente
- status de pagamento manual consegue ser acompanhado
- jogadores conseguem criar e entrar em partidas abertas

O MVP nao deve tentar provar:

- progressao completa de jogador
- paginas publicas de atleta
- torneios
- integracao com gateway de pagamento
- geolocalizacao e busca por proximidade
- gestao completa de Academy, coaches, aulas e CRM de alunos
- rede social
- comportamento Web3/token

## Modelo De Entrega

Construir o MVP em fatias verticais.

Cada fatia deve definir:

- comportamento de produto
- entidades e regras de backend
- rascunho de contrato de API
- telas e estados do frontend
- necessidades para Figma
- issues do Jira
- gate de integracao

O backend pode andar uma fatia na frente, mas nenhum modulo importante deve ser
considerado completo de produto antes do fluxo de frontend validar o formato da
API.

## Ordem Recomendada Dos Modulos

1. Autenticacao e acesso a conta
2. Fundacao do app frontend
3. Perfil do jogador
4. Onboarding de Organization e Academy
5. Gestao de quadras
6. Calendario de disponibilidade
7. Descoberta de quadras
8. Reservas
9. Pagamentos manuais
10. Planos de Academy
11. Partidas abertas
12. Preparacao para lancamento

## Tipos De Usuario

### Visitante

Visitante e uma pessoa nao autenticada.

Visitantes podem:

- acessar a tela de entrada
- usar Google Sign-In ou Google One Tap
- talvez navegar em uma descoberta publica, se o produto aprovar

Visitantes nao podem:

- reservar quadras
- entrar em partidas abertas
- criar partidas abertas
- acessar ferramentas de Organization
- atualizar perfis

Decisao aberta:

- se a descoberta publica de quadras existe antes do login.

### Jogador

Jogador e um usuario autenticado que pode reservar quadras e participar da
comunidade.

Jogadores podem:

- entrar e sair da conta
- completar um perfil basico
- escolher esporte principal
- informar nivel simples por esporte
- descobrir quadras
- solicitar reservas
- ver historico de reservas
- criar partidas abertas
- entrar e sair de partidas abertas

Jogadores nao podem no MVP:

- gerenciar atributos detalhados de progressao
- publicar pagina publica de jogador
- receber conquistas/rankings
- usar fluxos de carteira/Web3
- criar torneios de Organizations

### Organization

Organization e uma arena, clube, escola, operador de quadras ou organizador.

Organizations podem:

- completar perfil de Organization
- criar e gerenciar quadras
- ativar e desativar quadras
- definir esportes suportados
- definir precos e regras necessarias para reserva
- publicar horarios disponiveis
- ver agenda por dia ou semana
- ver detalhes de reservas
- confirmar ou cancelar reservas
- atualizar status de pagamento manualmente

Organizations nao podem no MVP:

- gerenciar alunos e mensalidades
- gerenciar coaches/aulas
- usar relatorios financeiros avancados
- criar torneios
- criar partidas abertas oficiais do Organization

### Academy

Academy e o contexto de aulas, coaches, alunos, turmas e planos.

Academies podem no MVP:

- existir como contexto inicial escolhido no onboarding
- cadastrar planos com nome, preco, validade em dias corridos e limites de uso
- vender ou conceder manualmente um plano a um Player existente
- registrar e corrigir consumo manualmente
- acompanhar validade, usos restantes e pagamento manual

Academies nao podem no MVP:

- gerenciar turmas completas
- gerenciar coaches
- aceitar alunos em aulas
- gerenciar CRM de alunos externos
- automatizar cobranca recorrente e inadimplencia

Decisao de escopo:

- Academy e independente de Organization; uma Academy nao precisa pertencer a
  uma Organization para existir.

### Admin App

Admin App nao e uma area padrao do MVP.

Admin App deve ser introduzido somente se houver um fluxo operacional real que
precise de revisao manual, suporte ou moderacao controlada.

Decisao aberta:

- se o lancamento precisa de uma ferramenta interna minima de Admin App ou se pode
  ser operado inicialmente por suporte/processos internos.

## Conceitos Compartilhados

### Esportes

Esportes do MVP:

- `futevolei`
- `beach_tennis`
- `beach_volleyball`

Regras:

- usar `futevolei` como nome interno do produto
- modelar esportes genericamente como `Sport`
- deixar `altinha` fora do MVP
- evitar hardcode que impeça novos esportes depois

Implicacoes no frontend:

- seletor de esporte deve suportar os tres esportes do MVP
- labels devem ser legiveis para humanos
- filtros de esporte devem funcionar em descoberta, quadras, perfil e partidas
  abertas

Implicacoes no backend:

- APIs devem expor identificadores estaveis de esporte
- quadra, perfil, disponibilidade, reserva e partida aberta devem referenciar o
  mesmo conceito de esporte

Decisao aberta:

- se esportes do MVP serao seed records no banco ou enums expostos por endpoint.

### Nivel Simples Do Jogador

Nivel simples e permitido no MVP apenas como atributo de matchmaking/filtro.

Regras:

- o nivel e autodeclarado
- o nivel nao e uma pontuacao verificada
- o nivel nao e o sistema V2 de distribuicao de skills
- o nivel pode ser usado como expectativa em partidas abertas
- o nivel pode ajudar jogadores a encontrarem jogos adequados
- no MVP o jogador tem exatamente um esporte principal
- esportes adicionais sao capacidade futura paga e nao aparecem como lista ativa
  no contrato MVP

Sugestao inicial de niveis:

- `beginner`
- `intermediate`
- `advanced`

Decisao:

- no MVP o nivel pertence ao esporte principal do jogador.

### Visibilidade De Status E Estado

O MVP deve deixar estados explicitos. O usuario nao deve precisar adivinhar
etapas escondidas do fluxo.

Status principais:

- reserva: `pending_payment`, `confirmed`, `canceled`, `expired`, `completed`
- pagamento: `pending`, `paid`, `failed`, `overdue`
- partida aberta: `open`, `full`, `canceled`, `completed`
- quadra: `active`, `inactive`

Implicacoes no frontend:

- usar badges ou labels compactas de status
- mostrar acoes desabilitadas quando o status bloquear uma acao
- mostrar mensagens de regra de negocio em linguagem simples
- nao expor detalhes internos de provider, banco ou infraestrutura

Implicacoes no backend:

- status deve ser validado em toda transicao
- transicoes invalidas devem retornar `business_rule_violation`
- recursos inexistentes devem retornar `resource_not_found`
- acesso cruzado entre Organizations deve retornar `forbidden`

### Data, Hora E Dinheiro

O MVP precisa de tratamento consistente para reservas e disponibilidade.

Regras:

- reserva e disponibilidade devem referenciar data, hora inicial e hora final
- horarios de quadra devem ser comparaveis sem ambiguidade
- agenda do Organization deve suportar visao por dia e por semana
- precos devem estar visiveis antes da solicitacao de reserva
- valores monetarios devem ser armazenados em centavos/unidade menor no backend,
  a menos que outro padrao seja aprovado

Implicacoes no frontend:

- usar exibicao amigavel para Brasil por padrao
- mostrar claramente inicio e fim do horario
- evitar datas relativas ambiguas em confirmacoes de reserva
- mostrar preco antes do jogador solicitar a reserva

Implicacoes no backend:

- validar se horario inicial e anterior ao horario final
- validar duracao do horario se o produto definir duracoes permitidas
- validar limites de data/hora de forma consistente
- evitar armazenamento monetario com ponto flutuante

Decisoes abertas:

- politica de timezone do MVP
- duracao permitida dos horarios de reserva
- se precos variam por dia/hora ou se o MVP comeca com preco simples por quadra
- janela de cancelamento

## Comportamento Compartilhado De API E Erros

Toda entrada e saida publica deve usar DTOs com Zod ou validacao equivalente.

Codigos publicos esperados:

- `validation_error`
- `unauthorized`
- `forbidden`
- `resource_not_found`
- `conflict`
- `business_rule_violation`
- `internal_error`

Regras:

- formato invalido de entrada retorna `validation_error`
- requests nao autenticados retornam `unauthorized`
- acesso cruzado entre Organizations retorna `forbidden`
- entidades inexistentes retornam `resource_not_found`
- duplicidade ou falha de unicidade retorna `conflict` quando apropriado
- requests validos bloqueados por regra de produto retornam
  `business_rule_violation`
- detalhes de provider ou infraestrutura nao devem vazar em respostas publicas

UX de erro no frontend:

- `validation_error`: erros inline nos campos sempre que possivel
- `business_rule_violation`: mensagem contextual perto da acao
- `unauthorized`: redirecionar para login ou estado de sessao expirada
- `forbidden`: mostrar estado de limite de acesso
- `resource_not_found`: mostrar tela/estado de nao encontrado
- `internal_error`: mostrar mensagem generica de tentar novamente/suporte

## Modulo 1: Autenticacao E Acesso A Conta

### Objetivo Do MVP

Permitir que usuarios autentiquem com Google e recebam a mesma sessao interna do
Sandicts usada pelos demais metodos de autenticacao.

Google Sign-In e Google One Tap sao os metodos principais de entrada com baixo
atrito.

### Incluido

- Google Sign-In
- Google One Tap
- criacao de sessao
- refresh/preservacao de sessao
- sign-out
- hidratacao da sessao/conta autenticada
- erros publicos seguros, sem detalhes do provider

### Fora Do Escopo

- Apple login
- login com senha
- recuperacao de senha
- escopos do Google Calendar
- autorizacao de calendario no frontend

### Fluxo No Frontend

Tela de entrada:

1. visitante abre `/sign-in`
2. pagina carrega o script Google Identity Services
3. usuario pode clicar em Google Sign-In
4. usuario pode receber prompt de Google One Tap
5. frontend recebe credential ou ID token do Google
6. frontend envia credential ao backend
7. backend cria sessao Sandicts
8. frontend hidrata estado autenticado
9. usuario cai na rota pos-login

Roteamento recomendado pos-login:

- se perfil do jogador estiver incompleto, ir para onboarding de perfil
- se perfil estiver completo, ir para home do jogador
- se usuario tiver acesso de Organization e estava entrando na area de Organization, ir
  para dashboard do Organization

Estados do frontend:

- carregando script do Google
- request de login em andamento
- One Tap nao exibido/pulado de forma silenciosa ou com telemetria
- erro generico de credencial invalida
- sessao expirada
- usuario ja autenticado

### Comportamento No Backend

Backend deve:

- aceitar credential ou ID token do Google
- validar issuer
- validar audience/client ID vindo de config tipada
- validar expiracao
- validar assinatura
- validar subject
- validar email verificado
- usar `sub` do Google como chave de identidade do provider
- nunca usar email do Google como chave de identidade do provider
- vincular identidade Google verificada a conta existente apenas conforme
  politica de autenticacao
- criar sessao interna Sandicts com sucesso

Detalhes especificos do provider nao devem aparecer em mensagens publicas.

### Conceitos De Dados

Principais:

- `Account`
- `AuthSession`
- `ExternalIdentity`

Campos esperados de identidade externa:

- provider, por exemplo `google`
- subject do provider, vindo do `sub` do Google
- account id
- snapshot de email do provider, se util
- timestamps

Regras:

- provider + subject do provider deve ser unico
- subject do provider nao e email
- email pode ser usado apenas em politica de vinculacao quando verificado

### Rascunho De API

Endpoint de auth conhecido:

- `POST /auth/google/sign-in`

Endpoints necessarios ou esperados:

- `POST /auth/refresh`
- `POST /auth/sign-out`
- `GET /auth/session`

Os nomes exatos podem mudar para seguir convencoes do backend, mas o frontend
precisa de contratos estaveis antes da implementacao.

### Regras De Negocio

- token Google invalido retorna `unauthorized`
- email Google nao verificado e rejeitado
- identidade Google existente autentica a conta vinculada
- nova identidade Google verificada cria conta e sessao
- identidade Google verificada pode vincular a conta existente com mesmo email
  apenas conforme politica de auth
- sign-out invalida a sessao atual
- refresh nao deve criar outra conta

### Necessidades Para Figma

Telas/componentes:

- tela de entrada
- botao Google Sign-In
- permissao/posicionamento de One Tap
- estado de carregamento
- erro generico de autenticacao
- estado de sessao expirada
- confirmacao de saida, se o produto quiser

Nao desenhar como caminho padrao do MVP:

- formulario de senha
- esqueci minha senha
- Apple sign-in
- prompt de autorizacao de calendario

### Gate De Integracao

Auth esta pronto quando:

- usuario consegue entrar com botao Google
- usuario consegue entrar com One Tap
- usuario consegue preservar/renovar sessao
- usuario consegue sair
- token invalido mostra erro seguro
- email nao verificado e rejeitado
- frontend e backend concordam sobre hidratacao da sessao

## Modulo 2: Fundacao Do App Frontend

### Objetivo Do MVP

Criar a casca do frontend que sustentara todas as telas do MVP sem prender o
produto a suposicoes de V2.

### Incluido

- fundacao Next.js
- TypeScript
- App Router
- shadcn/ui
- Tailwind CSS
- lucide-react
- TanStack Query para server state
- Zod com React Hook Form para formularios
- cliente OpenAPI gerado a partir do Swagger do Nest
- Zustand apenas para estado local de UI
- Playwright para E2E
- Vitest com Testing Library para componentes/hooks
- validacao de ambiente
- cliente de API com credenciais
- layout publico
- layout protegido do jogador
- layout protegido de Organization
- layout protegido de Academy
- reserva de layout/rota para Admin App quando houver fluxo operacional real
- modelo de navegacao
- estados de loading, vazio, erro, proibido e nao encontrado
- direcao de componentes base e tokens visuais

### Fora Do Escopo

- polimento final de marca
- design system completo
- animacoes avancadas
- site de marketing completo

### Comportamento No Frontend

O app deve ter estas areas conceituais:

- area publica
- area do jogador
- area de Organization
- area de Academy com planos e acessos do MVP
- Admin App reservado para operacao interna quando houver necessidade real

Rascunho de rotas:

- `/sign-in`
- `/app`
- `/app/profile`
- `/app/discovery`
- `/app/courts/[courtId]`
- `/app/reservations`
- `/app/open-matches`
- `/app/open-matches/[matchId]`
- `/app/open-matches/new`
- `/organizations/[organizationSlug]/manage`
- `/organizations/[organizationSlug]/manage/profile`
- `/organizations/[organizationSlug]/manage/courts`
- `/organizations/[organizationSlug]/manage/courts/new`
- `/organizations/[organizationSlug]/manage/availability`
- `/organizations/[organizationSlug]/manage/agenda`
- `/organizations/[organizationSlug]/manage/reservations/[reservationId]`
- `/organizations/[organizationSlug]/manage/payments`
- `/academies/[academySlug]/manage`
- `/academies/[academySlug]/manage/plans`
- `/academies/[academySlug]/manage/plans/new`
- `/academies/[academySlug]/manage/access`
- `/admin-app`

Decisao aberta:

- os nomes de rota podem mudar depois do Figma e da criacao do repo frontend.

### Dependencias De Backend

- health endpoint
- endpoint de sessao/auth
- politica estavel de CORS/cookies
- Swagger/OpenAPI ou contratos documentados

### Necessidades Para Figma

Telas/componentes:

- shell global do app
- navegacao do jogador
- navegacao de Organization
- navegacao mobile
- template de estado vazio
- template de estado de erro
- template de loading
- componente de badge/status
- padrao de validacao de campo

### Gate De Integracao

Fundacao do frontend esta pronta quando:

- frontend roda localmente
- frontend consegue chamar backend
- rotas protegidas redirecionam corretamente
- shell separa areas de jogador e Organization
- estados base existem e podem ser reutilizados

## Modulo 3: Perfil Do Jogador

### Objetivo Do MVP

Permitir que jogadores autenticados completem o perfil minimo necessario para
descoberta, reservas e partidas abertas.

### Incluido

- nome de exibicao
- esporte principal
- nivel simples no esporte principal
- estado de completude do perfil
- edicao de perfil basico

### Fora Do Escopo

- perfil publico
- upload de foto
- bio
- nacionalidade
- lado de jogo
- pe dominante
- fundamentos/recursos detalhados
- orcamento e distribuicao de skills
- historico de evolucao do jogador

### Fluxo No Frontend

Onboarding:

1. jogador entra
2. app verifica completude do perfil
3. se incompleto, mostra onboarding de perfil
4. jogador confirma nome de exibicao
5. jogador escolhe esporte principal
6. jogador escolhe nivel simples nesse esporte
7. app salva perfil
8. jogador vai para home do jogador

Edicao:

1. jogador abre tela de perfil
2. jogador edita campos permitidos no MVP
3. app valida campos obrigatorios
4. backend salva alteracoes
5. app reflete perfil atualizado

Estados do frontend:

- perfil incompleto
- salvando
- erros de validacao
- salvo
- falha ao carregar perfil

### Comportamento No Backend

Backend deve:

- expor perfil atual do jogador
- criar ou atualizar perfil basico do jogador
- validar se esporte e suportado
- validar se nivel e suportado
- impedir jogador de editar perfil de outro jogador
- expor completude de perfil para roteamento no frontend

### Conceitos De Dados

Principais:

- `PlayerProfile`
- `Sport`
- nivel simples do esporte principal

Campos recomendados:

- account id
- nome de exibicao
- esporte principal
- nivel do esporte principal
- timestamps

### Rascunho De API

Possiveis endpoints:

- `GET /players/me`
- `POST /players/me`
- `PATCH /players/me`
- `GET /sports`

### Regras De Negocio

- conta autenticada pode ter um perfil de jogador
- perfil MVP tem exatamente um esporte principal
- multi-esporte e uma capacidade futura paga
- perfil deve referenciar esportes suportados no MVP
- nivel simples e autodeclarado
- nivel de perfil nao e ranking
- perfil nao deve exigir geolocalizacao

### Necessidades Para Figma

Telas/componentes:

- onboarding de perfil
- edicao de perfil
- seletor de esporte
- seletor de nivel
- prompt de perfil incompleto
- estados de sucesso e erro ao salvar

### Gate De Integracao

Perfil do jogador esta pronto quando:

- jogador recem-autenticado e enviado para onboarding
- jogador consegue escolher esporte principal
- jogador consegue escolher nivel simples
- jogador consegue salvar e recarregar perfil
- esporte ou nivel invalido e rejeitado claramente

## Modulo 4: Onboarding De Organization E Academy

### Objetivo Do MVP

Permitir que um Organization crie o perfil minimo para expor quadras e que uma
Academy crie o perfil minimo para oferecer planos.

### Incluido

- conta/perfil de Organization
- identidade de arena, clube, local ou organizador
- informacoes publicas necessarias para descoberta
- detalhes operacionais de contato, se necessarios
- entrada no dashboard do Organization
- conta/perfil independente de Academy
- identidade e contato minimo da Academy
- entrada no dashboard da Academy

### Fora Do Escopo

- coaches
- aulas
- CRM de alunos externos
- cobranca recorrente
- ERP escolar completo
- relatorios avancados

### Fluxo No Frontend

Setup de Organization:

1. usuario autenticado abre area de Organization
2. se nao houver perfil de Organization, mostrar estado de configuracao
3. usuario informa nome do Organization e dados obrigatorios de listagem
4. usuario salva perfil do Organization
5. app redireciona para dashboard do Organization

Dashboard do Organization:

- mostrar progresso de setup
- mostrar quantidade de quadras
- mostrar resumo da agenda do dia
- mostrar indicadores de reservas/pagamentos pendentes quando existirem

Setup de Academy:

1. usuario autenticado abre area de Academy
2. se nao houver perfil de Academy, mostrar estado de configuracao
3. usuario informa nome e dados minimos da Academy
4. usuario salva perfil da Academy
5. app redireciona para dashboard da Academy

Dashboard da Academy:

- mostrar progresso de setup
- mostrar quantidade de planos ativos
- mostrar resumo de acessos ativos ou perto de expirar
- mostrar pagamentos pendentes quando existirem

Estados do frontend:

- sem perfil de Organization
- carregando perfil de Organization
- area de Organization proibida
- salvando perfil
- perfil salvo
- sem perfil de Academy
- carregando perfil de Academy
- area de Academy proibida

### Comportamento No Backend

Backend deve:

- criar perfil de Organization
- ler perfil do Organization do usuario autenticado
- atualizar perfil do Organization
- garantir limites de ownership/acesso
- impedir acesso a dados de outro Organization
- criar, ler e atualizar perfil de Academy
- impedir acesso a dados de outra Academy

### Conceitos De Dados

Principais:

- `Organization`
- `OrganizationUser` ou conceito equivalente de vinculo/ownership
- `Academy`
- `AcademyUser` ou conceito equivalente de vinculo/ownership

Campos recomendados:

- nome de exibicao
- tipo/categoria, se necessario
- descricao ou resumo de listagem, se aprovado
- endereco/localizacao textual necessaria para listagem
- campos de contato operacional
- timestamps

Decisao aberta:

- campos exatos de localizacao do Organization antes de geolocalizacao entrar em V2.

### Rascunho De API

Possiveis endpoints:

- `GET /organizations/me`
- `POST /organizations`
- `PATCH /organizations/me`
- `GET /academies/me`
- `POST /academies`
- `PATCH /academies/me`

### Regras De Negocio

- Organization e o conceito para arena, local, clube ou organizador
- Academy e o conceito independente para escola, treino, alunos e planos
- usuarios de Organization acessam apenas dados do proprio Organization
- acesso cruzado entre Organizations retorna `forbidden`
- perfil de Organization deve existir antes da criacao de quadra
- usuarios de Academy acessam apenas dados da propria Academy
- acesso cruzado entre Academies retorna `forbidden`
- perfil de Academy deve existir antes da criacao de plano

### Necessidades Para Figma

Telas/componentes:

- setup de Organization
- shell do dashboard de Organization
- formulario de perfil de Organization
- estado de Organization ausente
- estado de acesso proibido
- setup e formulario de perfil de Academy
- shell do dashboard de Academy
- estados de Academy ausente e acesso proibido

### Gate De Integracao

Onboarding de Organization e Academy esta pronto quando:

- usuario consegue criar perfil de Organization
- usuario consegue criar perfil de Academy independente de Organization
- dashboard carrega depois do setup
- dados do Organization ficam restritos ao Organization autenticado
- dados da Academy ficam restritos a Academy autenticada
- estados de ausente/proibido ficam claros

## Modulo 5: Gestao De Quadras

### Objetivo Do MVP

Permitir que Organizations criem e gerenciem as quadras que jogadores poderao
descobrir e reservar.

### Incluido

- criacao de quadra
- edicao de quadra
- lista de quadras
- ativacao/desativacao de quadra
- esportes suportados
- informacao de preco
- regras simples visiveis para jogadores

### Fora Do Escopo

- galeria de fotos
- avaliacoes/reviews
- catalogo rico de comodidades, salvo se necessario ao MVP
- motor automatico de precificacao
- motor completo de regras de agenda, salvo se aprovado

### Fluxo No Frontend

Criacao de quadra:

1. Organization abre gestao de quadras
2. Organization cria nova quadra
3. Organization define nome
4. Organization seleciona esportes suportados
5. Organization define informacao de preco
6. Organization adiciona regras simples da quadra
7. Organization salva
8. quadra aparece na lista de quadras do Organization

Ativacao:

1. Organization abre lista/detalhe de quadra
2. Organization alterna quadra ativa/inativa
3. quadra inativa nao pode ser reservada
4. quadra inativa continua visivel para o Organization

Estados do frontend:

- lista de quadras vazia
- criando quadra
- editando quadra
- quadra inativa
- selecao de esporte invalida
- falha ao salvar

### Comportamento No Backend

Backend deve:

- criar quadra no Organization autenticado
- atualizar quadra do Organization autenticado
- listar quadras do Organization
- expor campos publicos/seguros de quadra para descoberta
- aplicar elegibilidade de reserva por ativo/inativo
- aplicar acesso escopado por Organization

### Conceitos De Dados

Principais:

- `Court`
- `CourtSport`
- `CourtPrice` ou equivalente
- `CourtRule` ou campo textual de regras

Campos recomendados:

- Organization id
- nome
- esportes suportados
- flag/status ativo
- preco em centavos ou referencia de regra de preco
- regras simples
- timestamps

### Rascunho De API

Possiveis endpoints:

- `GET /organizations/courts`
- `POST /organizations/courts`
- `GET /organizations/courts/:courtId`
- `PATCH /organizations/courts/:courtId`
- `PATCH /organizations/courts/:courtId/status`

Descoberta pode usar endpoints publicos separados de leitura.

### Regras De Negocio

- Organization gerencia apenas suas proprias quadras
- quadra deve suportar pelo menos um esporte do MVP
- quadras inativas nao podem ser reservadas
- quadras inativas nao devem aparecer como reservaveis na descoberta
- preco/regras da quadra devem aparecer antes da solicitacao de reserva

### Necessidades Para Figma

Telas/componentes:

- lista de quadras
- lista vazia
- formulario de criacao de quadra
- formulario de edicao de quadra
- seletor de esportes suportados
- campo de preco
- campo de regras da quadra
- controle ativo/inativo
- badge de status da quadra

### Gate De Integracao

Gestao de quadras esta pronta quando:

- Organization consegue criar quadra
- Organization consegue editar quadra
- Organization consegue desativar quadra
- quadra inativa nao pode ser reservada
- quadra aparece na lista do Organization

## Modulo 6: Calendario De Disponibilidade

### Objetivo Do MVP

Permitir que Organizations publiquem horarios disponiveis de quadras e vejam a
agenda operacional.

### Incluido

- criar horario disponivel
- editar horario disponivel
- excluir ou desativar horario disponivel
- visao de agenda por dia
- visao de agenda por semana
- prevencao de horario invalido/sobreposto

### Fora Do Escopo

- motor de recorrencia, salvo aprovacao explicita
- sincronizacao avancada de calendario
- integracao com Google Calendar
- atribuicao de funcionarios

### Fluxo No Frontend

Publicacao de disponibilidade:

1. Organization abre calendario de disponibilidade
2. Organization seleciona quadra
3. Organization seleciona data
4. Organization seleciona hora inicial e final
5. Organization salva horario
6. horario aparece na agenda dia/semana
7. horario pode aparecer na descoberta para jogadores

Agenda:

- mostrar horarios disponiveis
- mostrar estado de reserva por horario quando houver reserva
- permitir leitura rapida por dia/semana

Estados do frontend:

- nenhuma quadra criada
- nenhuma disponibilidade
- editor de horario
- erro de sobreposicao
- faixa de horario invalida
- horario salvo
- agenda carregando

### Comportamento No Backend

Backend deve:

- criar disponibilidade para quadra do Organization
- atualizar disponibilidade
- excluir/desativar disponibilidade quando permitido
- impedir faixas de horario invalidas
- impedir sobreposicao de disponibilidade na mesma quadra se o produto escolher
  unicidade estrita de slot
- expor disponibilidade para descoberta
- proteger dados escopados por Organization

### Conceitos De Dados

Principal:

- `AvailabilitySlot`

Campos recomendados:

- Organization id
- court id
- sport id, se o slot for especifico por esporte
- data
- hora inicial
- hora final
- status/flag ativo
- override de preco, se aprovado
- timestamps

Decisoes abertas:

- se disponibilidade e por quadra ou por esporte
- se um horario suporta multiplos esportes quando a quadra suporta multiplos
- se horarios sobrepostos sao sempre proibidos ou permitidos em algum caso

### Rascunho De API

Possiveis endpoints:

- `GET /organizations/availability`
- `POST /organizations/availability`
- `PATCH /organizations/availability/:slotId`
- `DELETE /organizations/availability/:slotId`
- `GET /organizations/agenda`

### Regras De Negocio

- Organizations sao a fonte da verdade para disponibilidade
- disponibilidade deve referenciar quadra do Organization
- horarios indisponiveis nao podem ser reservados
- reservas confirmadas devem bloquear o horario
- Organization nao pode criar disponibilidade para quadra de outro Organization

### Necessidades Para Figma

Telas/componentes:

- calendario de disponibilidade
- seletor de quadra
- editor de horario
- agenda por dia
- agenda por semana
- exibicao de status do slot
- erro de sobreposicao
- estado sem quadras

### Gate De Integracao

Disponibilidade esta pronta quando:

- Organization consegue publicar horario
- horario aparece na agenda
- horario invalido e rejeitado
- horario fica disponivel para descoberta/reserva

## Modulo 7: Descoberta De Quadras

### Objetivo Do MVP

Permitir que jogadores encontrem quadras por filtros simples e escolham um
horario candidato para reserva.

### Incluido

- filtro por esporte
- filtro por disponibilidade
- filtro por preco
- ver resumo/perfil do Organization
- ver detalhe da quadra
- ver horarios disponiveis

### Fora Do Escopo

- busca por proximidade/geolocalizacao
- avaliacoes e reviews
- comparacao rica de localizacao
- galeria de fotos
- recomendacao automatica

### Fluxo No Frontend

Descoberta:

1. jogador abre descoberta
2. jogador escolhe esporte
3. jogador opcionalmente escolhe data/hora
4. jogador opcionalmente filtra por preco
5. app mostra resultados de Organizations/quadras
6. jogador abre detalhe da quadra
7. jogador escolhe horario disponivel
8. jogador inicia solicitacao de reserva

Estados do frontend:

- estado inicial de descoberta
- carregando resultados
- nenhum resultado
- filtro alterado
- detalhe de quadra carregando
- slot selecionado ficou indisponivel
- usuario nao autenticado se descoberta publica existir, mas reserva exigir login

### Comportamento No Backend

Backend deve:

- consultar quadras ativas com disponibilidade ativa
- filtrar por esporte suportado
- filtrar por disponibilidade
- filtrar por preco
- retornar DTOs seguros para descoberta
- excluir quadras inativas de resultados reservaveis
- nao expor dados privados do Organization

### Conceitos De Dados

Leitura a partir de:

- `Organization`
- `Court`
- `AvailabilitySlot`
- `Reservation`
- `Payment` quando necessario para estado de reserva, nao necessariamente para
  listagem

### Rascunho De API

Possiveis endpoints:

- `GET /discovery/courts`
- `GET /discovery/courts/:courtId`
- `GET /discovery/courts/:courtId/availability`

### Regras De Negocio

- descoberta deve mostrar apenas quadras ativas e reservaveis
- filtros devem ser simples e previsiveis
- quadras inativas nao podem aparecer como reservaveis
- reservas confirmadas devem remover/bloquear o horario
- descoberta nao exige geolocalizacao no MVP

### Necessidades Para Figma

Telas/componentes:

- tela de descoberta do jogador
- filtro de esporte
- filtro de data/hora
- filtro de preco
- card de resultado
- estado sem resultados
- detalhe da quadra
- lista de horarios disponiveis
- CTA de reserva

### Gate De Integracao

Descoberta esta pronta quando:

- jogador consegue filtrar por esporte
- jogador consegue filtrar por disponibilidade
- jogador consegue filtrar por preco
- quadras inativas/indisponiveis nao aparecem como reservaveis
- jogador consegue ir de um resultado para solicitacao de reserva

## Modulo 8: Reservas

### Objetivo Do MVP

Permitir que jogadores solicitem reservas e Organizations confirmem ou cancelem,
evitando duplicidade de reservas confirmadas no mesmo horario.

### Incluido

- solicitacao de reserva pelo jogador
- status de reserva
- confirmacao pelo Organization
- cancelamento pelo jogador
- cancelamento pelo Organization
- historico de reservas do jogador
- detalhe de reserva para Organization
- prevencao de duplicidade de slot confirmado

### Fora Do Escopo

- confirmacao automatica por gateway de pagamento
- automacao complexa de politica de cancelamento
- reembolsos
- lista de espera
- reservas recorrentes

### Fluxo No Frontend

Solicitacao pelo jogador:

1. jogador seleciona horario disponivel da quadra
2. jogador revisa Organization, quadra, esporte, data, hora, preco e regras
3. jogador envia solicitacao de reserva
4. app mostra status da reserva
5. jogador consegue ver reserva no historico

Confirmacao pelo Organization:

1. Organization abre agenda ou detalhe da reserva
2. Organization revisa solicitacao
3. Organization confirma ou cancela a reserva
4. reserva confirmada bloqueia o horario
5. jogador ve estado atualizado

Cancelamento:

1. jogador ou Organization abre a reserva
2. ator permitido solicita cancelamento
3. backend valida status e politica de cancelamento
4. reserva vira `canceled`

Estados do frontend:

- solicitacao em andamento
- pagamento pendente
- confirmada
- cancelada
- expirada
- concluida
- erro de slot duplicado
- cancelamento nao permitido

### Comportamento No Backend

Backend deve:

- criar reserva para jogador autenticado
- referenciar Organization, quadra, esporte, data, hora inicial, hora final,
  jogador, estado de pagamento e status de reserva
- validar se slot existe e esta disponivel
- validar se quadra esta ativa
- impedir duas reservas confirmadas ativas para mesma quadra/horario
- permitir Organization confirmar reserva
- permitir jogador ou Organizations cancelar conforme politica
- expor historico de reservas do jogador
- expor detalhe/agenda de reservas do Organization

### Conceitos De Dados

Principais:

- `Reservation`
- `ReservationStatus`

Campos recomendados:

- Organization id
- court id
- sport id
- player id
- availability slot id, se modelado diretamente
- data
- hora inicial
- hora final
- snapshot de preco
- referencia/status de pagamento
- status
- timestamps

### Rascunho De API

Possiveis endpoints:

- `POST /reservations`
- `GET /players/me/reservations`
- `GET /organizations/reservations/:reservationId`
- `PATCH /organizations/reservations/:reservationId/confirm`
- `PATCH /reservations/:reservationId/cancel`
- `PATCH /organizations/reservations/:reservationId/cancel`

### Regras De Negocio

- status de reserva deve sempre ser explicito
- reserva confirmada bloqueia o horario da quadra
- mesma quadra/horario nao pode ter duas reservas confirmadas ativas
- reserva nao pode ser criada para quadra inativa
- reserva nao pode ser criada para horario indisponivel
- Organization gerencia apenas suas reservas
- jogador ve apenas seu historico
- janela de cancelamento esta aberta e precisa ser decidida antes da
  implementacao final

### Necessidades Para Figma

Telas/componentes:

- revisao de reserva
- sucesso/status de reserva
- historico de reservas do jogador
- badge de status de reserva
- detalhe de reserva do Organization
- acoes de confirmar/cancelar
- mensagem de slot duplicado
- mensagem de cancelamento bloqueado

### Gate De Integracao

Reservas estao prontas quando:

- jogador consegue solicitar reserva
- Organization consegue confirmar
- reserva confirmada bloqueia horario
- duplicidade de reserva confirmada e rejeitada
- jogador e Organization veem status atualizado
- cancelamento permitido funciona

## Modulo 9: Pagamentos Manuais

### Objetivo Do MVP

Acompanhar estado de pagamento manualmente, sem integracao com gateway.

### Incluido

- registro de status de pagamento
- reserva le status de pagamento
- acesso de plano de Academy le status de pagamento
- Organization ve pagamentos pendentes
- Organization atualiza status manualmente
- Academy ve e atualiza pagamentos de seus planos manualmente
- mudancas de status sao controladas e auditaveis

### Fora Do Escopo

- gateway de pagamento
- reembolso como fluxo operacional
- splits
- repasses
- comissao automatica
- cobranca recorrente e inadimplencia automatizada

### Fluxo No Frontend

Gestao de pagamento pelo Organization:

1. Organization abre pagamentos pendentes
2. Organization ve status de pagamento da reserva
3. Organization abre detalhe do pagamento/reserva
4. Organization marca pagamento como pago, falhou ou vencido quando permitido
5. telas de reserva refletem novo estado

Gestao de pagamento pela Academy:

1. Academy abre os acessos concedidos ou vendidos
2. Academy ve o status de pagamento relacionado
3. Academy atualiza o status manualmente quando permitido
4. Player e Academy veem o novo estado

Visibilidade para jogador:

- jogador ve status de pagamento na reserva
- jogador ve status de pagamento no acesso de Academy
- jogador nao paga via gateway Sandicts no MVP

Estados do frontend:

- pagamento pendente
- pago
- falhou
- vencido
- atualizacao manual em andamento
- atualizacao bloqueada por status

### Comportamento No Backend

Backend deve:

- criar ou associar pagamento a reserva
- criar ou associar pagamento a acesso de plano de Academy
- expor status de pagamento nas leituras de reserva
- expor status de pagamento nas leituras de acesso de Academy
- permitir atualizacoes manuais escopadas por Organization ou Academy
- validar transicoes permitidas
- manter referencias de provider opcionais para futuro gateway
- nao exigir dados de gateway no MVP

### Conceitos De Dados

Principais:

- `Payment`
- `PaymentStatus`

Campos recomendados:

- reservation id
- Academy plan access id, quando aplicavel
- Organization ou Academy id
- status
- valor em centavos
- moeda
- metadados de atualizacao manual, se disponiveis
- campos opcionais de provider para futuro
- timestamps

### Rascunho De API

Possiveis endpoints:

- `GET /organizations/payments`
- `PATCH /organizations/payments/:paymentId/status`
- `GET /organizations/reservations/:reservationId/payment`
- `GET /academies/:academySlug/payments`
- `PATCH /academies/:academySlug/payments/:paymentId/status`

### Regras De Negocio

- status de pagamento deve ser explicito
- atualizacoes manuais sao escopadas por Organization ou Academy
- Organization nao pode atualizar pagamentos de outro Organization
- Academy nao pode atualizar pagamentos de outra Academy
- campos de provider sao opcionais no MVP
- `refunded` nao e status operacional do MVP sem fluxo de reembolso definido

### Necessidades Para Figma

Telas/componentes:

- lista de pagamentos pendentes
- badge de status de pagamento
- controle manual de status
- confirmacao de atualizacao
- erro de atualizacao
- resumo de pagamento na reserva
- resumo de pagamento no acesso de Academy

### Gate De Integracao

Pagamentos manuais estao prontos quando:

- Organization ve pagamentos pendentes de reservas
- Organization atualiza status manualmente
- reserva reflete status de pagamento
- atualizacao de pagamento entre Organizations e proibida
- Academy atualiza apenas pagamentos de seus proprios acessos
- acesso de plano reflete status de pagamento

## Modulo 10: Planos De Academy

### Objetivo Do MVP

Permitir que uma Academy represente ofertas reais de treino sem depender de
nomes fixos como "mensal" e controle manualmente quem ainda pode usar o plano.

### Incluido

- plano com nome e descricao definidos pela Academy
- preco e moeda
- validade em dias corridos
- limite total de usos opcional
- limite semanal de usos opcional
- plano ativo ou inativo
- venda ou concessao manual para Player existente
- data de ativacao e expiracao calculada
- snapshot das regras no momento da concessao
- consumo e correcao manual auditavel
- status agendado, ativo, esgotado, expirado e cancelado
- pagamento manual relacionado

### Fora Do Escopo

- Player externo sem conta Sandicts
- agenda de aulas
- reserva de turma
- coaches
- check-in automatico
- renovacao automatica
- pausa ou congelamento
- cupom, prorata, split e repasse
- transferencia de usos
- ERP escolar completo

### Fluxo No Frontend

Cadastro do plano:

1. Academy abre gestao de planos
2. Academy informa nome, preco e validade em dias corridos
3. Academy escolhe acesso ilimitado, limite semanal, pacote de usos ou uma
   combinacao de limites
4. app valida a configuracao
5. Academy salva e ativa o plano

Venda ou concessao:

1. Academy escolhe um plano ativo
2. Academy seleciona um Player existente
3. Academy informa a data de ativacao
4. app mostra a data final e copia das regras comerciais
5. Academy confirma o acesso e o pagamento manual relacionado

Consumo:

1. Academy abre o acesso do Player
2. app informa se mais um uso e permitido
3. Academy registra um uso
4. app atualiza total restante e limite da janela atual
5. uma correcao posterior restaura o uso sem apagar o historico

Visibilidade do Player:

- nome do plano e Academy
- inicio e expiracao
- status do acesso e pagamento
- usos totais e restantes, quando limitados
- limite e consumo da janela semanal, quando limitados

### Comportamento No Backend

Backend deve:

- criar, editar, listar, ativar e inativar planos escopados por Academy
- validar dias de validade e limites como inteiros positivos
- tratar ausencia de limite total e semanal como acesso ilimitado
- criar acesso com snapshot de nome, preco, moeda, validade e limites
- calcular expiracao por dias corridos na timezone da Academy
- calcular janelas consecutivas de sete dias desde a ativacao
- impedir consumo quando acesso nao estiver ativo
- impedir consumo acima do limite total ou semanal
- registrar consumo e correcao de forma auditavel
- derivar ou manter status de acesso de forma explicita
- impedir acesso cruzado entre Academies

### Conceitos De Dados

Principais:

- `AcademyPlan`
- `AcademyPlanAccess`
- `AcademyPlanUsage`
- `Payment`

Campos recomendados do plano:

- Academy id
- nome e descricao
- valor e moeda
- dias de validade
- limite total opcional
- limite semanal opcional
- ativo
- timestamps

Campos recomendados do acesso:

- Academy e Player ids
- AcademyPlan de origem
- snapshot de nome, valor, moeda, validade e limites
- data de ativacao e expiracao
- status
- Payment relacionado, quando aplicavel
- timestamps

### Rascunho De API

Possiveis endpoints:

- `GET /academies/:academySlug/plans`
- `POST /academies/:academySlug/plans`
- `PATCH /academies/:academySlug/plans/:planId`
- `GET /academies/:academySlug/plan-access`
- `POST /academies/:academySlug/plan-access`
- `GET /academies/:academySlug/plan-access/:accessId`
- `POST /academies/:academySlug/plan-access/:accessId/usages`
- `POST /academies/:academySlug/plan-access/:accessId/usage-corrections`
- `GET /players/me/academy-plan-access`

### Regras De Negocio

- nome do plano nao determina comportamento
- validade usa dias corridos e a data de ativacao conta como primeiro dia
- plano de 28 dias iniciado em 1 de julho vale ate o fim de 28 de julho
- limite semanal usa janelas de sete dias ancoradas na ativacao
- quando limites total e semanal existem, ambos devem ser respeitados
- usos nao consumidos expiram e nao acumulam
- editar/inativar plano nao altera acessos existentes
- somente Academy dona pode alterar plano, acesso, uso e pagamento
- plano gratuito pode ter valor zero

Regras detalhadas:

- `docs/product/sandicts-academy-plan-model.md`

### Necessidades Para Figma

Telas/componentes:

- lista de planos ativos e inativos
- formulario de plano com presets de uso
- resumo de regras antes de salvar
- lista de acessos por Player
- concessao/venda de plano
- detalhe de acesso e consumo
- indicador de usos restantes e janela semanal
- acao de registrar uso e confirmar correcao
- estados agendado, ativo, esgotado, expirado e cancelado

### Gate De Integracao

Planos de Academy estao prontos quando:

- Academy representa os cinco exemplos da especificacao de planos
- editar plano nao altera um acesso ja criado
- expiracao de 28 dias e calculada por dias corridos
- limite total e semanal bloqueiam consumo corretamente
- consumo corrigido preserva historico
- Player ve seus limites, saldo, validade e statuses
- acesso entre Academies e proibido

## Modulo 11: Partidas Abertas

### Objetivo Do MVP

Permitir que jogadores conectem uma partida a uma reserva real, preencham as
vagas usando o Sandicts e os grupos que ja possuem e retornem para remarcar.

A fonte de verdade detalhada e
`docs/product/sandicts-open-match-model.md`.

### Incluido

- partida aberta criada por jogador
- intencao `social`, `competitive` ou `training`
- visibilidade `public` ou `invite_only`
- politica de nivel simples `open`, `similar` ou `range`
- esporte, data e hora
- limite de participantes
- local vinculado a uma reserva Sandicts ou local manual
- entrada imediata em partida publica com vaga
- pedido de aprovacao em partida `invite_only`
- fila de espera quando habilitada
- participante registrado pode sair
- criador pode aprovar, recusar ou remover quando a politica permitir
- convidado sem conta gerenciado pelo criador
- link estavel, copiar link e compartilhamento nativo/WhatsApp
- landing page com dados seguros e retorno a partida depois do login
- atualizacoes operacionais essenciais e lembrete pre-jogo
- criador cancelar partida
- confirmacao leve de realizacao e acoes de remarcar/repetir
- estados aberta/cheia/cancelada/concluida
- sugestao deterministica de Players como incremento nao bloqueante depois do
  fluxo principal

### Fora Do Escopo

- partidas abertas criadas por Organization
- convite direcionado dentro do app
- importacao de contatos ou integracao com API do WhatsApp
- visibilidade por grupo
- reivindicacao de convidado por uma conta
- chat
- matchmaking automatico
- rankings
- recompensas
- rating ou reputacao publica
- recomendacao por IA

### Fluxo No Frontend

Criar partida:

1. jogador abre criacao de partida aberta
2. jogador escolhe intencao e visibilidade
3. jogador escolhe uma reserva propria ou informa um local manual
4. no modo reserva, app herda esporte, quadra, data e horario
5. no modo manual, jogador informa esporte, local, data e horario
6. jogador define capacidade e politica de nivel
7. jogador decide se habilita fila de espera, se configuravel
8. app cria a partida com o criador confirmado
9. jogador ve detalhe e pode compartilhar o link

Entrar em partida publica:

1. jogador abre lista de partidas abertas
2. jogador filtra por esporte, intencao ou nivel, se disponivel
3. jogador abre detalhe da partida
4. app autentica o jogador sem perder o destino, se necessario
5. jogador entra como confirmado enquanto existe vaga
6. se estiver cheia e a fila estiver ativa, jogador entra como `waitlisted`
7. app atualiza quantidade de confirmados e vagas restantes

Pedir entrada por link `invite_only`:

1. jogador abre link compartilhado
2. landing mostra apenas dados seguros e o estado atual
3. app autentica o jogador sem perder o destino, se necessario
4. jogador envia pedido e fica `requested`
5. criador confirma ou recusa
6. confirmacao ocupa vaga e pode deixar a partida `full`

Gerenciar convidado sem conta:

1. criador informa somente um nome de exibicao
2. convidado entra confirmado e ocupa uma vaga
3. apenas o criador pode editar ou remover o convidado
4. sistema nao cria `User`, `Player`, perfil, rating ou historico publico

Sair/cancelar:

- participante pode sair antes do cutoff definido
- criador nao pode sair da partida ativa; sem transferencia de ownership no MVP,
  deve cancelar
- criador pode remover participante antes do cutoff definido
- criador pode cancelar a partida
- cancelar partida nao cancela a reserva
- reserva vinculada cancelada ou expirada antes do jogo cancela a partida

Concluir:

1. depois do horario, criador recebe uma confirmacao leve
2. criador informa se a partida aconteceu e pode registrar `no_show` conforme a
   politica ainda a definir
3. detalhe concluido oferece remarcar a quadra ou criar partida semelhante

Estados do frontend:

- aberta
- cheia
- cancelada
- concluida
- aguardando aprovacao
- confirmado
- na fila de espera
- recusado
- saiu ou foi removido
- no-show
- entrada bloqueada
- saida bloqueada
- reserva aguardando pagamento/confirmacao
- local manual sem confirmacao de disponibilidade

### Comportamento No Backend

Backend deve:

- criar partida aberta para jogador autenticado
- listar partidas abertas
- ler detalhe de partida
- validar reserva, ownership e compatibilidade quando houver vinculo
- impedir mais de uma partida ativa para a mesma reserva
- herdar esporte, quadra e horario da reserva
- permitir confirmacao direta em partida publica com vaga
- criar pedido em partida `invite_only`
- permitir criador confirmar ou recusar pedido
- permitir fila de espera quando a partida estiver cheia e a fila estiver ativa
- permitir saida de participante
- permitir criador gerenciar convidado sem conta
- impedir participacao ativa duplicada
- impedir confirmacao acima da capacidade
- impedir entrada quando cancelada ou concluida
- atualizar status para cheia quando limite for atingido
- reabrir quando uma vaga confirmada for liberada
- promover fila de espera conforme politica definida
- cancelar a partida se a reserva for cancelada ou expirar antes do jogo
- publicar link estavel e metadados seguros sem efeitos colaterais no `GET`
- emitir eventos para atualizacoes operacionais e metricas
- permitir criador cancelar
- concluir conforme politica e preservar historico

### Conceitos De Dados

Principais:

- `OpenMatch`
- `OpenMatchParticipant`
- `OpenMatchGuest`

Campos recomendados de `OpenMatch`:

- creator/player id
- sport id
- intent
- visibility
- level policy e intervalo opcional
- place mode
- reservation id ou campos de local manual
- start/end date-time
- participant capacity
- waitlist enabled
- status
- public share identifier/slug
- timestamps

Campos recomendados de participante registrado:

- match id
- player id
- status
- requested/confirmed/left/no-show timestamps

Campos recomendados de convidado:

- match id
- display name
- status confirmado/removido/no-show
- managed-by creator id

Decisoes abertas antes da implementacao:

- cutoffs de saida, remocao e cancelamento
- autoridade e contestacao de `no_show`
- fila sempre ativa ou configuravel pelo criador
- canais e horarios dos avisos
- indexacao publica/unlisted e campos de preview
- responsabilidade por custo e futura divisao de pagamento
- politica de conclusao da partida
- opt-in de descoberta para sugestoes simples

### Rascunho De API

Possiveis endpoints:

- `GET /open-matches`
- `POST /open-matches`
- `GET /open-matches/:matchId`
- `POST /open-matches/:matchId/participations`
- `DELETE /open-matches/:matchId/participants/me`
- `PATCH /open-matches/:matchId/participants/:participantId`
- `POST /open-matches/:matchId/guests`
- `PATCH /open-matches/:matchId/guests/:guestId`
- `DELETE /open-matches/:matchId/guests/:guestId`
- `PATCH /open-matches/:matchId/cancel`
- `PATCH /open-matches/:matchId/complete`
- `GET /open-matches/:matchId/suggestions`

### Regras De Negocio

- criador e o primeiro participante confirmado
- jogador nao pode ter participacao ativa duplicada
- apenas confirmados e convidados confirmados ocupam capacidade
- partida publica cheia usa fila quando habilitada; caso contrario bloqueia
- partida `invite_only` exige aprovacao do criador
- jogador nao pode entrar em partida cancelada ou concluida
- partida expoe vagas totais, participantes confirmados e vagas restantes
- nivel esperado e filtro/expectativa, nao prova verificada de habilidade
- local manual nao bloqueia agenda nem prova disponibilidade
- campos derivados da reserva nao podem divergir
- cancelar partida nao cancela reserva
- cancelar ou expirar reserva antes do jogo cancela partida vinculada
- convidado nao cria conta ou perfil implicitamente
- leitura de landing/preview nao altera participacao
- criador pode cancelar a propria partida

### Necessidades Para Figma

Telas/componentes:

- lista de partidas abertas
- filtros por esporte, intencao e nivel simples
- detalhe de partida aberta
- criacao em passos com intencao, visibilidade, local e capacidade
- selecao entre reserva e local manual
- exibicao de participantes, pedidos, fila, convidados e vagas
- acao de entrar ou pedir participacao
- acao de sair
- acao de cancelar
- gestao de pedidos e convidados pelo criador
- compartilhar/copiar link/WhatsApp
- landing publica e retorno depois do login
- estados da reserva vinculada
- confirmacao pos-jogo e acoes de remarcar/repetir
- badges cheia/cancelada/concluida
- erros de duplicidade, capacidade, autorizacao e cutoff

### Gate De Integracao

Partidas abertas estao prontas quando:

- jogador cria partida vinculada a reserva e com local manual
- campos da reserva permanecem sincronizados e cancelamento/expiracao propaga
- outro jogador entra em partida publica ou pede aprovacao em `invite_only`
- duplicidade e excesso de capacidade sao bloqueados
- fila e liberacao de vaga funcionam conforme a politica escolhida
- criador gerencia pedidos e convidado sem conta
- participante sai e criador cancela conforme cutoffs
- link compartilhado preserva privacidade e retorna ao fluxo depois do login
- avisos operacionais essenciais e conclusao/remarcar funcionam
- metricas principais sao registradas

## Modulo 12: Preparacao Para Lancamento

### Objetivo Do MVP

Deixar o MVP confiavel o suficiente para lancamento controlado e validacao com
usuarios reais.

### Incluido

- smoke tests dos fluxos criticos
- cobertura de validacao de API
- checagens de auth/sessao
- checagens de acesso proibido
- observabilidade basica
- estrategia de seed data
- documentacao de variaveis de ambiente
- prontidao de deploy

### Fora Do Escopo

- otimizacao de grande escala
- plataforma Admin App completa
- analytics avancado
- automacao completa de suporte

### Fluxos Criticos

Lancamento deve validar:

- Google sign-in e One Tap
- onboarding de perfil
- setup de Organization
- setup de Academy
- criacao de quadra
- publicacao de disponibilidade
- filtros de descoberta
- solicitacao e confirmacao de reserva
- atualizacao de pagamento manual
- cadastro, concessao, consumo e expiracao de plano de Academy
- criar/entrar/sair de partida aberta
- sign-out/expiracao de sessao

### Checagens De Backend

Deve cobrir:

- erros de validacao
- erros de regra de negocio
- acesso proibido entre Organizations
- acesso proibido entre Academies
- acesso nao autenticado
- prevencao de reserva duplicada
- bloqueio de consumo de plano expirado, esgotado ou acima do limite semanal
- prevencao de entrada duplicada em partida aberta

### Checagens De Frontend

Deve cobrir:

- layout mobile dos fluxos principais
- loading states
- empty states
- exibicao de erro de regra de negocio
- sessao expirada
- redirects de rotas protegidas

### Gate De Integracao

Preparacao para lancamento esta pronta quando:

- todos os gates dos modulos do MVP passam
- validacao de CI passa
- ambiente de deploy esta configurado
- secrets/config de producao estao documentados fora do repo
- decisoes abertas estao resolvidas ou aceitas explicitamente como limitacoes
  de lancamento

## Inventario Para Figma

Use esta lista para criar as primeiras paginas/frames no Figma.

### Fundacoes

- tokens visuais
- tipografia
- botoes
- inputs
- select/combobox
- date picker
- slot de calendario
- badge/status
- card/list item
- modal/dialog
- estado vazio
- estado de erro
- estado de carregamento
- navegacao mobile
- navegacao desktop

### Auth

- entrada/login
- botao Google
- estado de One Tap
- sessao expirada
- erro de auth

### Jogador

- home do jogador
- onboarding de perfil
- edicao de perfil
- descoberta
- detalhe de quadra
- solicitacao de reserva
- status de reserva
- historico de reservas
- lista de partidas abertas
- detalhe de partida aberta
- criar partida aberta

### Organization

- setup de Organization
- dashboard do Organization
- lista de quadras
- criar/editar quadra
- calendario de disponibilidade
- agenda dia
- agenda semana
- detalhe de reserva
- lista de pagamentos
- atualizacao manual de pagamento

### Academy

- setup e dashboard da Academy
- lista e formulario de planos
- concessao/venda manual de plano
- lista e detalhe de acessos por Player
- consumo e correcao manual
- validade, saldo e status de pagamento

## Formato Do Roadmap No Jira

Epicos recomendados:

- `[MVP] Authentication and Account Access`
- `[Frontend] Web App Foundation`
- `[MVP] Player Profile`
- `[MVP] Organization and Academy Onboarding`
- `[MVP] Court Management`
- `[MVP] Availability`
- `[MVP] Discovery`
- `[MVP] Reservations`
- `[MVP] Manual Payments`
- `[MVP] Academy Plans`
- `[MVP] Open Matches`
- `[MVP] Launch Hardening`

Formato recomendado:

```text
Epic: [MVP] Reservations
  Story: [Reservations] Player requests a court reservation
    Subtask: [Backend] Implement reservation request behavior
    Subtask: [Frontend] Build reservation request flow
    Subtask: [E2E] Validate reservation request happy path
```

Regras:

- criar epicos como ancoras de roadmap
- criar tarefas detalhadas apenas para o modulo atual e o proximo
- criar tarefas de decisao, prototipo e documentacao antes de implementar
  paginas que ainda nao tem formato aprovado
- manter tarefas de frontend e backend sob a mesma story de produto quando
  possivel
- adicionar validacao E2E/integracao para cada gate de modulo

Para o roadmap detalhado de frontend/fullstack, incluindo tarefas de decisao,
prototipo, documentacao, fundacao, implementacao e validacao, usar
`fradelli/reactjs-sandicts-web:docs/frontend/sandicts-mvp-delivery-roadmap.md`.

## Decisoes Abertas Atuais

Resolver antes do Figma final e implementacao do modulo afetado.

Decidido:

- stack frontend: Next.js App Router com TypeScript
- UI: shadcn/ui, Tailwind CSS e lucide-react
- server state: TanStack Query
- formularios: Zod e React Hook Form
- API client: gerado a partir do Swagger/OpenAPI do Nest
- estado local de UI: Zustand somente quando necessario
- testes: Playwright para E2E, Vitest e Testing Library para componentes/hooks

Aberto:

- local do repositorio frontend
- versao do Node.js para frontend
- package manager
- gerador OpenAPI exato
- modelo de navegacao jogador vs Organization
- descoberta publica antes do login
- fonte da verdade da sessao e endpoint de hidratacao
- politica de timezone do MVP
- duracao permitida de slots de reserva
- modelo de preco para quadras e disponibilidade
- janela de cancelamento
- se disponibilidade e por quadra ou por esporte
- se local da partida aberta e referencia a quadra, texto livre ou ambos
- se ferramenta Admin App e necessaria para lancamento

## Proximo Passo Imediato

Usar este documento para uma rodada de planejamento nesta ordem:

1. registrar tarefas de docs para as decisoes ja tomadas e docs nao commitados
2. revisar decisoes abertas e preencher as necessarias antes do Figma
3. criar ou ajustar epicos no Jira
4. detalhar primeiro apenas Auth, Fundacao do Frontend e Perfil do Jogador
5. criar prototipos/fluxos de Figma para Auth e Perfil do Jogador
6. implementar integracao de Auth e Perfil do Jogador como primeiras fatias
   fullstack
7. continuar modulo por modulo usando este mesmo formato
