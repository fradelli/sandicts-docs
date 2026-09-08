---
title: Plano de implementação da KAN-223
doc-type: implementation-guide
status: implemented
last-reviewed: 2026-09-07
owners:
  - frontend
  - design-system
related-issue: KAN-223
related-repositories:
  - fradelli/design-system
  - fradelli/sandicts-docs
  - fradelli/reactjs-sandicts-web
---

# KAN-223 — contrato visual e governança do Design System

## Status do documento

- **Escopo:** estabelecer a fonte normativa, o ownership e as regras de evolução
  do Design System antes do scaffold técnico.
- **História:** `KAN-223 — [Design System] Establish shared visual contract and governance`.
- **Épico:** `KAN-219 — [Platform] Shared Design System`.
- **Repositórios-alvo:** `fradelli/design-system`, `fradelli/sandicts-docs` e
  `fradelli/reactjs-sandicts-web`.
- **Repositório excluído desta história:** `fradelli/kaizen-app`; seu alinhamento
  local pertence à KAN-227 e depende da E04-T02 do roadmap do Kaizen.
- **Prontidão do plano:** aprovado e executado em 2026-09-07.
- **Prontidão para copiar e colar:** o contrato documental está concluído; o scaffold técnico pertence à KAN-222.
- **Evidência:** repositório GitHub público inicializado em `main` com ADR e governança; descrição atualizada da KAN-223; guia arquitetural compartilhado; documentação de transição do frontend Sandicts.
- **Fora de escopo:** package.json, dependências, tokens executáveis, CSS,
  Storybook, componentes, CI, publicação e mudanças visuais nos aplicativos.

## Objetivo

Ao concluir a KAN-223, qualquer mudança futura deve conseguir responder sem
ambiguidade:

1. qual repositório é a fonte normativa do Design System;
2. o que pertence e o que não pertence a `@fradelli/ui`;
3. como Inter, dark-first e a paleta aprovada são governados;
4. quando um componente pode ser compartilhado;
5. como versões, deprecações e breaking changes serão decididas;
6. qual história implementará cada etapa seguinte.

A entrega é exclusivamente documental. KAN-222 inicia o scaffold técnico depois
que este contrato estiver aprovado.

## Decisão arquitetural a registrar

### Fonte normativa

`fradelli/design-system/docs/decisions/0001-shared-design-system-foundation.md`
será a fonte normativa da arquitetura e governança do package. Os repositórios
consumidores documentam somente sua decisão de consumo e apontam para esse ADR;
eles não copiam a matriz inteira de regras.

### Contrato inicial

- repositório: `fradelli/design-system`;
- package recomendado: `@fradelli/ui`;
- um único package enquanto não existirem ciclos de release distintos;
- dark-first no primeiro release;
- Inter variável como família proporcional comum;
- vermelho coral como identidade;
- amarelo solar como ação principal;
- oito cores pastéis categóricas;
- React/TypeScript, Tailwind 4, shadcn Radix Nova e Phosphor como direção técnica;
- SemVer, Changesets e upgrades independentes por consumidor;
- sem Next.js, domínio, API, autenticação, rotas ou providers no package.

### Ownership

| Item | Owner |
| --- | --- |
| tokens, tipografia, radius, foco, motion e status genéricos | `@fradelli/ui` |
| primitives visuais e testes acessíveis | `@fradelli/ui` |
| padrão visual genérico comprovado em dois produtos | `@fradelli/ui` |
| logo, nome, metadata e arte específica | aplicativo |
| shell, página, rota e composição de feature | aplicativo |
| reserva, quadra, treino, dieta, hábito e tarefa | aplicativo |
| API, Prisma, React Query, i18n e autenticação | aplicativo |
| mapeamento entre cor categórica e domínio | aplicativo |

## Ordem de implementação

| Ordem | Repositório | Ação | Caminho | Resultado |
| ---: | --- | --- | --- | --- |
| 1 | design-system | Create | `README.md` | bootstrap público e propósito do repositório |
| 2 | design-system | Create | `docs/decisions/0001-shared-design-system-foundation.md` | ADR normativo |
| 3 | design-system | Create | `docs/governance/component-admission.md` | regra objetiva para compartilhar componentes |
| 4 | design-system | Create | `docs/governance/versioning-and-releases.md` | SemVer, deprecação e release ownership |
| 5 | design-system | Create | `AGENTS.md` | contrato operacional para trabalho assistido |
| 6 | sandicts-docs | Edit | `docs/engineering/shared-design-system-implementation-guide.md` | registrar repo existente e ADR canônico |
| 7 | reactjs-sandicts-web | Edit | `docs/frontend/sandicts-frontend-tech-decisions.md` | separar primitives compartilhados de features locais |
| 8 | reactjs-sandicts-web | Edit | `docs/frontend/sandicts-mvp-visual-system.md` | distinguir estado atual da direção visual futura |
| 9 | Jira | Edit | `KAN-223` | substituir “futuro repositório” por link existente e registrar evidências |

Os arquivos 1 a 5 formam o primeiro commit do repositório vazio. Os arquivos 6
a 8 entram em PRs documentais nos repositórios já existentes. O card só deve ser
concluído depois que os três repositórios apontarem para o mesmo ADR.

## Mudanças detalhadas

### 1. `fradelli/design-system/README.md`

**Ação:** Create.

**Local exato:** arquivo inteiro.

**Problema atual:** o repositório está vazio; visitantes não conseguem saber seu
propósito, estado, consumidores ou limites.

**Mudança:** documentar propósito, status governance-only, consumidores,
non-goals, ADR normativo, roadmap Jira e aviso de que ainda não existe package
publicado.

**Motivo:** impede que o repositório vazio seja interpretado como biblioteca
pronta e estabelece navegação antes do scaffold.

**Verificação:** links para ADR, Jira e repositórios consumidores resolvem; o
README não contém comandos de instalação antes da publicação do package.

### 2. `fradelli/design-system/docs/decisions/0001-shared-design-system-foundation.md`

**Ação:** Create.

**Local exato:** arquivo inteiro.

**Problema atual:** as decisões estão distribuídas entre conversa, Jira e um
guia de implementação do Sandicts.

**Mudança:** registrar contexto, decisão, matriz de ownership, contrato visual,
contrato técnico, distribuição, versionamento, alternativas rejeitadas,
consequências, riscos e gatilho para revisão.

**Itens obrigatórios:**

- dark-first não significa proibir um modo light futuro;
- Inter é carregada pelos apps e exposta como `--font-inter`;
- vermelho de marca e destructive são tokens diferentes;
- cores categóricas não carregam significado de domínio no package;
- calendário completo só pode ser extraído após dois usos reais;
- cada app fixa sua versão e escolhe quando atualizar;
- mudanças incompatíveis exigem SemVer e migração documentada.

**Verificação:** o documento responde às seis perguntas do objetivo e não
contém implementação especulativa.

### 3. `fradelli/design-system/docs/governance/component-admission.md`

**Ação:** Create.

**Local exato:** arquivo inteiro.

**Mudança:** um componente só entra no package quando:

1. o nome descreve função visual, não domínio;
2. props não importam DTO, schema, rota ou status de um app;
3. funciona sem Next.js ou provider do consumidor;
4. diferenças são resolvidas por composição ou tokens;
5. existe uso real em dois produtos, salvo primitives básicos;
6. teclado, foco, disabled, loading e erro são testáveis;
7. a API pode ser mantida por SemVer.

Flags como `product`, `sandictsVariant` ou `kaizenVariant` reprovam a admissão.

**Verificação:** aplicar o checklist aos candidatos `Button`, `StatusBadge` e
`Calendar`; o primeiro passa como primitive, os dois últimos permanecem locais
no primeiro release.

### 4. `fradelli/design-system/docs/governance/versioning-and-releases.md`

**Ação:** Create.

**Local exato:** arquivo inteiro.

**Mudança:** registrar releases 0.x, critérios de patch/minor/major, período de
deprecação, changelog, imutabilidade de versão publicada, owner de aprovação e
rollback por pin da versão anterior.

**Limitação intencional:** não configurar Changesets nem registry nesta história;
o documento apenas governa a implementação da KAN-222 e da KAN-226.

**Verificação:** alterações de token, prop, export, foco e aparência possuem uma
classificação de release explícita.

### 5. `fradelli/design-system/AGENTS.md`

**Ação:** Create.

**Local exato:** arquivo inteiro.

**Mudança:** definir rota mínima de contexto, ADR obrigatório, limites de
domínio, política de secrets, idioma da documentação e regra de não adicionar
dependências ou abstrações sem consumidor.

**Motivo:** o repositório será trabalhado com Codex e precisa impedir que tarefas
futuras ignorem as fronteiras aprovadas.

**Verificação:** não inclui credenciais, caminhos pessoais nem instruções que
contradigam o ADR.

### 6. `fradelli/sandicts-docs/docs/engineering/shared-design-system-implementation-guide.md`

**Ação:** Edit.

**Local exato:** frontmatter, Status do documento, Decisão arquitetural,
mudanças do repositório Design System e decisões pendentes.

**Mudança:** substituir “repositório proposto” pelo repositório existente,
adicionar a URL canônica, apontar para o ADR 0001 e remover a criação do
repositório do escopo futuro. Manter package, registry e licença como decisões
pendentes enquanto não aprovados.

**Verificação:** o inventário e a ordem de implementação não tratam mais o repo
como inexistente.

### 7. `fradelli/reactjs-sandicts-web/docs/frontend/sandicts-frontend-tech-decisions.md`

**Ação:** Edit.

**Local exato:** seção UI And Styling.

**Mudança:** registrar o estado de transição: primitives e tokens migrarão para
`@fradelli/ui`; marca, shells, features, domínio e integrações permanecem no
Sandicts. Linkar o ADR normativo por URL cross-repository.

**Verificação:** a documentação não diz simultaneamente que todo primitive é
owned localmente e que o package é seu owner futuro.

### 8. `fradelli/reactjs-sandicts-web/docs/frontend/sandicts-mvp-visual-system.md`

**Ação:** Edit.

**Local exato:** ownership, tipografia, cores e governança.

**Mudança:** separar claramente:

- **estado atual:** Roboto/IBM Plex/Montserrat e tokens locais continuam ativos;
- **estado alvo:** Inter, dark-first, vermelho/amarelo e paleta categórica serão
  ativados pela KAN-224/KAN-225;
- **fonte futura:** foundations e primitives serão governed pelo ADR 0001;
- **ownership local:** marca e regras visuais exclusivas do Sandicts.

**Motivo:** uma tarefa documental não pode declarar como entregue uma migração
de código ainda inexistente.

**Verificação:** scripts e implementação atuais continuam corretamente descritos
até as histórias de migração serem concluídas.

### 9. `KAN-223`

**Ação:** Edit somente durante a execução aprovada.

**Mudança:** atualizar a lista de repositórios para indicar que
`fradelli/design-system` existe, adicionar o link do ADR e anexar links das PRs
ou commits de evidência. Não alterar escopo, prioridade ou parent.

**Verificação:** o card permanece filho de KAN-219 e só é concluído quando a
KAN-222 estiver efetivamente desbloqueada.

## Estratégia de Git e entrega

### Bootstrap do repositório vazio

Como não existe commit em `main`, não há base para abrir a primeira PR. A
exceção de bootstrap recomendada é criar um primeiro commit documental direto em
`main` contendo somente os arquivos 1 a 5.

Mensagem recomendada:

~~~text
[KAN-223] docs(governance): establish shared design system contract
~~~

Depois desse commit, proteção de branch e fluxo por PR passam a ser obrigatórios
a partir da KAN-222.

### Repositórios consumidores

Cada consumidor recebe uma branch e PR independentes, ambas vinculadas à
KAN-223. Não misturar mudanças dos três repositórios em um único commit local.

Branch recomendada:

~~~text
codex/KAN-223-shared-design-system-governance
~~~

## Validação

### Design System

~~~powershell
git diff --check
rg -n "Sandicts|Kaizen|@fradelli/ui|dark-first|Inter|SemVer" README.md docs AGENTS.md
~~~

Resultado esperado: todos os conceitos obrigatórios aparecem na fonte
normativa; não existem package.json, código, secrets ou comandos de publicação.

### Sandicts Docs

~~~powershell
git diff --check
rg -n "repositório proposto|repositório ainda não existe|futuro fradelli/design-system" docs/engineering/shared-design-system-implementation-guide.md
~~~

Resultado esperado: a busca não retorna afirmações obsoletas.

### Sandicts Frontend

~~~powershell
npm run visual-system:check
git diff --check
~~~

Resultado esperado: a documentação de transição não viola os checks que também
inspecionam resíduos visuais.

### Revisão cross-repository

1. abrir todos os links a partir do README;
2. confirmar que apenas o ADR 0001 contém a decisão completa;
3. confirmar que documentos consumidores usam links e não cópias divergentes;
4. verificar ausência de secrets, tokens, e-mails e caminhos locais;
5. verificar que Kaizen não foi alterado pela KAN-223.

## Critérios de aceite

- [ ] `fradelli/design-system` possui README, ADR e regras de governança.
- [ ] O ADR 0001 é a única fonte normativa completa.
- [ ] Ownership comum e local está explícito.
- [ ] Dark-first, Inter e paleta categórica estão registrados.
- [ ] Vermelho de marca e destructive permanecem separados.
- [ ] Critério de admissão impede componentes de domínio no package.
- [ ] Versionamento e breaking changes possuem regras observáveis.
- [ ] Sandicts Docs aponta para o repositório existente.
- [ ] Sandicts Frontend distingue estado atual e estado alvo.
- [ ] Kaizen não foi modificado nesta história.
- [ ] Nenhuma dependência ou implementação foi adicionada.
- [ ] Nenhum secret ou dado pessoal foi versionado.
- [ ] KAN-223 contém evidências e desbloqueia a KAN-222.

## Rollback

Como a entrega é documental, rollback consiste em reverter os commits/PRs da
KAN-223. Não apagar o repositório nem reescrever histórico. Se uma decisão mudar,
criar novo ADR que substitua o 0001; não editar silenciosamente uma decisão já
usada por releases.

## Decisões aprovadas para a execução

1. **Package:** `@fradelli/ui` é o nome permanente.
2. **Registry e visibilidade:** GitHub Packages, com package inicialmente privado.
3. **Licença:** o repositório permanece sem `LICENSE` por enquanto; distribuição pública exige decisão explícita posterior.
4. **Bootstrap:** o primeiro commit documental foi autorizado diretamente em `main`, por se tratar de repositório vazio.
5. **Base visual:** Inter variável, dark-first, vermelho coral, amarelo solar, oito cores pastéis, Tailwind 4, shadcn/ui Radix Nova e Phosphor.
6. **Jira:** a correção dos oito links `Blocks` invertidos permanece uma ação operacional separada e destrutiva, sujeita à confirmação no momento da remoção.
