---
title: Guia de implementação do Design System compartilhado
doc-type: implementation-guide
status: accepted
last-reviewed: 2026-09-07
owners:
  - frontend
  - design-system
related-repositories:
  - fradelli/reactjs-sandicts-web
  - fradelli/kaizen-app
  - fradelli/design-system
---

# Design System compartilhado - guia de implementação

## Status do documento

- **Escopo:** criar um Design System versionado para Sandicts e Kaizen sem unir os repositórios nem compartilhar domínio.
- **Repositório de documentação:** fradelli/sandicts-docs.
- **Repositórios consumidores:** fradelli/reactjs-sandicts-web e fradelli/kaizen-app.
- **Repositório existente:** [fradelli/design-system](https://github.com/fradelli/design-system).
- **Package aprovado:** `@fradelli/ui`.
- **Registry aprovado:** GitHub Packages, inicialmente privado.
- **Direção visual aprovada:** dark-first, superfícies neutras quase pretas,
  vermelho coral como identidade, amarelo solar como ação principal e oito cores
  pastéis categóricas.
- **Tipografia aprovada:** Inter variável para interface, títulos e wordmarks
  textuais; diferenciação por peso, tamanho e tracking, não por famílias
  concorrentes.
- **Prontidão do plano:** pronto para decomposição em tarefas e execução incremental.
- **Prontidão para copiar e colar código:** o contrato normativo está aprovado; o código permanece reservado à KAN-222, que fechará os pins exatos no primeiro lockfile.
- **Estado observado em 2026-09-07:** Sandicts possui o sistema visual atual em
  produção/desenvolvimento; Kaizen concluiu E04-T01 e mantém E04-T02 como tarefa
  READY; o repositório do Design System existe e contém o ADR normativo da KAN-223.
- **Evidência usada:** frontend Sandicts no workspace atual, scaffold e tarefa
  ativa do Kaizen, decisões aprovadas pelo proprietário e documentação oficial
  de Next.js, Tailwind CSS, shadcn/ui, React, Changesets e GitHub Packages.
- **Fora de escopo deste guia:** publicar package, configurar credenciais, migrar componentes ou alterar os apps sem a história correspondente.

## Objetivo

Criar uma única fonte versionada para a identidade visual comum dos produtos:

- paleta dark comum e tokens semânticos acessíveis;
- Inter variável e tipografia por papel;
- radius, bordas, sombras, foco e motion;
- primitives shadcn/ui customizados;
- padrões visuais sem dependência de domínio;
- contratos de acessibilidade e de React Server Components.

Sandicts e Kaizen continuam com ciclos independentes de desenvolvimento, teste,
release e deploy. Cada app escolhe quando atualizar @fradelli/ui e preserva a
versão instalada em seu próprio lockfile.

## Decisão arquitetural

A fonte normativa é o [ADR 0001 do repositório Design System](https://github.com/fradelli/design-system/blob/main/docs/decisions/0001-shared-design-system-foundation.md). Este guia organiza a implementação; em caso de divergência, o ADR governa a arquitetura e o ownership.

### Forma escolhida

~~~text
fradelli/design-system
  -> @fradelli/ui
       -> Sandicts
       -> Kaizen
       -> futuros produtos do ecossistema
~~~

O repositório possui um único package. Tokens, componentes e helpers internos
não recebem packages separados enquanto não tiverem consumidores, ownership ou
ciclos de release diferentes.

### Fronteira de ownership

| Camada | Owner | Exemplos |
| --- | --- | --- |
| Identidade visual comum | @fradelli/ui | cores, tipografia, radius, foco, status e motion |
| Primitive visual | @fradelli/ui | Button, Input, Alert, Dialog e Skeleton |
| Padrão visual sem domínio | @fradelli/ui, após dois usos reais | LoadingRegion, PendingButton e DateNavigator |
| Marca do produto | app | logo, nome, metadata e artwork |
| Composição de página | app | shell, navegação e layout da feature |
| Domínio | app | reserva, quadra, refeição, treino, hábito e tarefa |
| Integração | app | API, Prisma, Server Actions, React Query, i18n e autenticação |

Regra normativa:

> Compartilhar identidade, tokens, primitives e comportamento visual genérico;
> manter marca, composição de página, integrações e regras de negócio em cada
> produto.

### Fundação visual aprovada

#### Tipografia

Inter variável é a única família proporcional da interface. O sistema usa a
mesma família com papéis diferentes:

| Papel | Peso inicial | Uso |
| --- | ---: | --- |
| body | 400 | conteúdo, formulários e textos corridos |
| label | 500 | labels, metadados e navegação |
| emphasis | 600 | botões, títulos de cards e estados ativos |
| heading | 700 | títulos de seção e página |
| display/brand | 800 | wordmark textual e destaques de marca |

Os apps carregam Inter com `next/font` e publicam a variável `--font-inter` no
elemento `html`. O package referencia essa variável e oferece fallback de
sistema. O package não importa `next/font`, não baixa fonte em runtime e não
inclui binários de fonte.

Sandicts deve remover Roboto, IBM Plex Sans e Montserrat somente depois que a
migração para Inter for comprovada por busca de imports, build e revisão visual.
O stack monoespaçado do sistema permanece reservado para dados técnicos; Inter
não substitui uma fonte mono quando a semântica exigir alinhamento de caracteres.

#### Cores

| Papel | Referência inicial | Contrato |
| --- | --- | --- |
| background | `#0B0B0C` | fundo dark principal |
| surface | `#141416` | cards, sidebar, popovers e calendário |
| elevated | `#1C1C1F` | superfícies elevadas e hover |
| border | `#2B2B30` | separação discreta entre superfícies |
| foreground | `#F4F4F5` | texto principal |
| muted foreground | `#9B9BA3` | texto secundário |
| brand | `#F07878` | vermelho coral de identidade e seleção |
| primary action | `#F2CF63` | amarelo solar para CTA e foco de atenção |

A paleta categórica inicial contém amarelo, laranja, vermelho, rosa, roxo,
azul, ciano e verde. Os valores aprovados na prévia são, respectivamente,
`#F2CF63`, `#F0A866`, `#F07878`, `#E58AB7`, `#B79AF2`, `#7DA7F2`,
`#65C8D0` e `#7BC8A4`.

O vermelho de marca não substitui o token `destructive`. Erros e exclusões usam
um token próprio, ícone, texto e `aria` aplicável para não depender somente de
cor. As cores categóricas são disponibilizadas pelo Design System, mas cada app
é owner do mapeamento entre cor e domínio. Por exemplo, o package conhece
`calendar.cyan`; ele não conhece `beach-tennis` nem `treino`.

#### Camadas de tokens

1. `primitive`: valores brutos de cor, espaço, radius, duração e tipografia;
2. `semantic`: função contextual como background, primary e destructive;
3. `component`: decisões restritas a um componente quando um token semântico
   geral não é suficiente.

Os tokens primitivos e semânticos ficam em JSON no formato DTCG, versionados no
repositório. CSS é artefato gerado e não deve ser editado manualmente.

### Decisões técnicas

1. **Base shadcn:** manter radix-nova, rsc: true, CSS variables e Phosphor,
   seguindo a base já validada no Sandicts. Stone pode continuar como metadado
   do gerador, mas os tokens customizados são a fonte visual em runtime.
2. **Tailwind:** manter a linha 4 e alinhar os dois consumidores na mesma versão
   testada pelo Design System.
3. **Tokens:** manter JSON DTCG como fonte versionada e gerar CSS/Tailwind por
   script determinístico verificado em CI.
4. **CSS:** o package publica um entrypoint de estilos com tokens, @theme
   inline, base layer e extensões shadcn necessárias. Os apps não duplicam o
   mapa de tokens.
5. **Detecção Tailwind:** cada app registra o dist do package com @source,
   pois Tailwind ignora node_modules por padrão.
6. **Build:** publicar ESM compilado e declarações TypeScript; não publicar TSX
   cru como único artefato.
7. **RSC:** preservar "use client" apenas nos entrypoints interativos. Não criar
   um barrel raiz que converta todo o catálogo em Client Components.
8. **Imports:** oferecer exports por componente, por exemplo
   @fradelli/ui/button, e um export CSS explícito.
9. **Framework:** o package não depende de Next.js, next/link, next-intl,
   React Query, Prisma ou APIs de produto.
10. **React:** react e react-dom são peerDependencies, impedindo uma cópia
   privada de React dentro da biblioteca.
11. **Catálogo:** Storybook documenta variantes, estados, dark mode,
    responsividade e exemplos acessíveis sem iniciar nenhum app consumidor.
12. **Versionamento:** Changesets registra intenção patch, minor ou major,
    atualiza changelog e conduz releases SemVer.
13. **Distribuição:** GitHub Packages usa o scope @fradelli; tokens de leitura
    ou publicação ficam somente nos cofres dos ambientes.
14. **Primeira estabilidade:** releases 0.x validam o contrato nos dois apps;
    1.0.0 marca a API suportada por ambos.

### API pública inicial

O primeiro catálogo deve ser baseado no código real do Sandicts, sem gerar um
catálogo especulativo:

| Item | Entrada inicial | Observação |
| --- | --- | --- |
| styles.css | Sim | tokens dark-first, Inter contract, radius, status, foco e layers |
| cn | Sim | composição determinística de classes |
| Alert | Sim | variants default, success, warning, info e destructive |
| Badge | Sim | variants visuais, sem mapear status de domínio |
| Button | Sim | variants e tamanhos atuais |
| Card | Sim | agrupamento visual sem semântica de negócio |
| Field | Sim | label, descrição, erro e agrupamento acessível |
| Input | Sim | controle HTML genérico |
| Label | Sim | primitive Radix |
| Separator | Sim | primitive Radix |
| Sheet | Sim | primitive genérico já usado e testado no Sandicts |
| Skeleton | Sim | loading visual com reduced motion |
| LoadingRegion | Depois do núcleo | candidato forte; validar no Kaizen antes de estabilizar |
| PendingButton | Depois do núcleo | candidato forte para gravações do Kaizen |
| PageState | Não inicialmente | composição e espaçamento ainda podem divergir |
| StatusBadge | Não inicialmente | API atual expõe diretamente o tipo de ícone Phosphor |
| Calendar/DateNavigator | Não inicialmente | extrair somente após existirem agendas reais nos dois apps |
| Dialog/AlertDialog | Quando houver consumidor | Kaizen precisa de confirmação, mas o primitive ainda não existe no catálogo real |
| Select/RadioGroup/Textarea | Quando houver consumidor | necessidade prevista, API visual ainda não validada |
| Table | Não | não há consumidor comum atual |

### Regra para novos componentes

Um item entra em @fradelli/ui somente quando todas as respostas forem positivas:

1. o nome descreve uma função visual, não um conceito de negócio;
2. as props não importam DTO, schema, rota ou status de um app;
3. o componente funciona sem Next.js e sem provider do consumidor;
4. tokens ou composição resolvem as diferenças entre produtos;
5. existe uso real ou necessidade documentada em pelo menos dois produtos, com
   exceção de primitives shadcn básicos;
6. comportamento de teclado, foco, disabled, loading e erro pode ser testado no
   package;
7. sua API pode ser mantida por SemVer.

Se uma diferença exigir flags como product, mode, sandictsVariant ou
kaizenVariant, o componente deve permanecer local.

## Ordem de implementação

Os identificadores abaixo são rótulos deste plano, não IDs de Jira nem do
roadmap do Kaizen.

| Ordem | ID | Repositório | Entrega | Gate de saída |
| ---: | --- | --- | --- | --- |
| 1 | DS-00 | sandicts-docs + apps | registrar decisão, ownership, paleta e Inter | documentos não contradizem a arquitetura escolhida |
| 2 | KAI-QA-01 | kaizen-app | concluir a E04-T02 já ativa | format, lint, tipos, testes e build disponíveis |
| 3 | DS-01 | design-system | criar package, build e CI mínimos no repositório existente | package vazio gera tarball ESM com tipos e CSS |
| 4 | DS-02 | design-system | implementar tokens DTCG, gerador CSS, Inter e Storybook Foundations | paleta dark renderiza e contraste é testado |
| 5 | DS-03 | design-system | migrar `cn` e primitives validados do Sandicts | catálogo e testes de componente passam |
| 6 | DS-04 | design-system | validar tarball em fixtures npm e pnpm | mesmo artefato funciona nos dois package managers |
| 7 | SAN-01 | reactjs-sandicts-web | instalar tarball e trocar imports mantendo overrides temporários | origem muda sem redesign acidental |
| 8 | SAN-02 | reactjs-sandicts-web | ativar Inter e a nova paleta; remover overrides legados | Sandicts usa vermelho/amarelo e paleta categórica |
| 9 | KAI-01 | kaizen-app | registrar nova decisão visual e corrigir o plano aprovado | E04-T04 depende explicitamente do package |
| 10 | KAI-02 | kaizen-app | instalar Tailwind 4, Inter e o mesmo tarball | scaffold renderiza primitives e estilos compartilhados |
| 11 | DS-05 | design-system + apps | publicar `0.1.0` e substituir tarball por versão exata | CI dos dois consumidores instala pelo registry |
| 12 | DS-06 | todos | operar releases 0.x e estabilizar API | `1.0.0` somente após uso real nos dois apps |
| 13 | CAL-01 | todos, futuramente | extrair primitives de agenda comprovadamente comuns | duas agendas reais validam a mesma API visual |

### Dependências do caminho crítico

~~~text
DS-00 -> DS-01 -> DS-02 -> DS-03 -> DS-04
                                   |-> SAN-01 -> SAN-02 -|
KAI-QA-01 -> KAI-01 --------------|-> KAI-02 ---------> DS-05 -> DS-06
                                                              -> CAL-01 (futuro)
~~~

Sandicts é o doador inicial dos primitives e o primeiro teste de compatibilidade.
Kaizen é o segundo consumidor e recebe a identidade nova diretamente. A
publicação `0.1.0` só ocorre depois que o tarball exato é aprovado em ambos; isso
separa erros de empacotamento de erros de autenticação no registry.

### Política de pull requests

Cada linha acima deve ser uma PR pequena e reversível. Não combinar na mesma PR:

- criação do package e redesign do Sandicts;
- troca de imports e remoção dos arquivos locais;
- adoção do Design System e implementação de uma feature;
- migração tipográfica e abstração de calendário;
- publicação no registry e alterações funcionais dos consumidores.

## Mudanças detalhadas por repositório

### 1. fradelli/sandicts-docs

#### ADR canônico cross-repository

**Estado:** concluído pela KAN-223.

**Fonte normativa:** [fradelli/design-system/docs/decisions/0001-shared-design-system-foundation.md](https://github.com/fradelli/design-system/blob/main/docs/decisions/0001-shared-design-system-foundation.md).

**Regra:** este repositório mantém o plano de implementação e as decisões específicas do Sandicts. A matriz completa de ownership, versionamento e admissão não deve ser duplicada aqui.

#### README.md

**Ação futura:** Edit.

**Local exato:** Purpose e lista de decisões cross-app.

**Mudança:** ampliar explicitamente o escopo para decisões do ecossistema que
envolvam Sandicts e produtos relacionados, e adicionar o link da decisão.

**Cuidado:** regras exclusivas do Kaizen continuam no repositório Kaizen.

### 2. fradelli/kaizen-app

E04-T01 já foi concluída e E04-T02 está READY. E04-T02 deve ser executada como
planejada para fornecer format, lint, typecheck e testes antes da integração.
Não adicionar Tailwind ou o package dentro dessa tarefa, porque isso misturaria
qualidade estática com uma mudança arquitetural ainda não registrada.

Depois de E04-T02, o Kaizen precisa de uma tarefa própria de adoção do Design
System, concluída antes de E04-T04 criar o shell. O repositório proíbe alterar
decisões aprovadas silenciosamente; portanto, documentação e roadmap fazem parte
da entrega, não são correção posterior.

#### docs/decisions/SHARED-DESIGN-SYSTEM.md

**Ação futura:** Create.

**Local exato:** arquivo inteiro.

**Mudança:** registrar que Tailwind 4 e @fradelli/ui substituem a decisão de CSS
Modules como fundação visual; registrar dark-first, Inter, ownership e política
de upgrades. Preservar Server Components como padrão e Client Components no
menor limite interativo.

#### docs/architecture/TARGET-ARCHITECTURE.md

**Ação futura:** Edit.

**Locais exatos:** tabela Stack escolhida e item Tailwind ou biblioteca de
componentes em Alternativas rejeitadas no P0.

**Mudança:** substituir CSS Modules + tokens globais por Tailwind 4 + package
versionado; remover a rejeição que deixou de representar a decisão do usuário.

#### docs/implementation/P0-IMPLEMENTATION-GUIDE.md

**Ação futura:** Edit.

**Locais exatos:** Decisões estruturais, Versões aprovadas, pacotes E04 e
validação acumulada.

**Mudança:** incluir Tailwind, PostCSS, @fradelli/ui, importação CSS,
carregamento das fontes e gates do package.

#### docs/implementation/tasks/E04-T01.md

**Ação futura:** Não editar para reescrever história.

**Motivo:** a tarefa está concluída e documenta corretamente o scaffold mínimo
que foi entregue. A mudança de arquitetura deve ser registrada por nova tarefa.

#### docs/implementation/tasks/E04-T02.md

**Ação futura:** executar sem ampliar o escopo.

**Resultado necessário:** disponibilizar os gates que a tarefa posterior do
Design System usará. Regras específicas de Tailwind/package entram na nova
tarefa, depois que a dependência existir.

#### Nova tarefa de adoção do Design System

**Ação futura:** Create conforme o protocolo do roadmap, sem reutilizar um ID
existente.

**Entradas mínimas:** decisão compartilhada, versão/tarball aprovado de
`@fradelli/ui`, `package.json`, `src/app/layout.tsx`, `src/app/globals.css`,
`src/app/page.tsx` e `src/app/page.module.css`.

**Entregáveis:**

- Tailwind 4 e PostCSS configurados;
- `@fradelli/ui` instalado por versão exata ou tarball durante homologação;
- CSS público do package importado uma vez;
- `@source` apontando para o dist do package;
- Inter carregada por `next/font` e exposta como `--font-inter`;
- `html` com dark mode explícito;
- página de fundação renderizada com Button/Card compartilhados;
- CSS Modules do scaffold removido apenas quando não possuir consumidor;
- gates de E04-T02, build e inspeção visual passando.

#### docs/implementation/tasks/E04-T04.md

**Ação futura:** Edit.

**Mudança:** o shell acessível usa primitives compartilhados, mas rotas Dieta
e Treino, copy e composição permanecem locais.

#### roadmap/ACTIVE.md e roadmap/README.md

**Ação futura:** Edit conforme o protocolo do repositório.

**Mudança:** inserir a nova tarefa depois de E04-T02 e torná-la dependência de
E04-T04. Manter E04-T01 como DONE e E04-T02 como próximo incremento até sua
conclusão.

#### package.json e pnpm-lock.yaml

**Ação futura:** Edit/Generate na nova tarefa.

**Mudança:** instalar Tailwind/PostCSS e `@fradelli/ui`; usar versão exata no
package manifest e lockfile reproduzível.

#### src/app/layout.tsx

**Ação futura:** Edit na nova tarefa.

**Mudança:** carregar Inter variável com `next/font`, publicar `--font-inter` e
aplicar dark mode no elemento raiz sem introduzir provider client-side.

#### src/app/globals.css

**Ação futura:** Edit na nova tarefa.

**Mudança:** remover os tokens light locais, importar Tailwind e o CSS do
package, registrar `@source` e preservar somente resets ou integrações realmente
específicos do Kaizen.

#### src/app/page.tsx e src/app/page.module.css

**Ação futura:** Edit e Remove quando o CSS Module não possuir consumidor.

**Mudança:** manter a página de fundação como smoke test, mas renderizá-la com
primitives e tokens compartilhados. Não antecipar dieta, treino ou agenda.

### 3. fradelli/reactjs-sandicts-web

#### docs/frontend/sandicts-frontend-tech-decisions.md

**Ação futura:** Edit.

**Local exato:** UI And Styling.

**Mudança:** substituir a regra que exige components owned exclusivamente no
frontend pela nova divisão: primitives em @fradelli/ui; feature components e
marca permanecem no Sandicts.

#### docs/frontend/sandicts-mvp-visual-system.md

**Ação futura:** Edit.

**Mudança:** transferir o contrato de tokens/primitives ao Design System e
manter neste documento somente aplicação da identidade no Sandicts, densidade
Player/Organization, marca e regras específicas. Substituir a orientação de
Roboto/IBM Plex/Montserrat por Inter e registrar vermelho/amarelo como direção
de marca.

#### package.json e package-lock.json

**Ação futura:** Edit/Generate.

**Mudança:** instalar uma versão exata de @fradelli/ui, remover dependências que
deixarem de ter consumidor local e manter Radix/CVA somente enquanto forem
usados diretamente por components locais.

**Regra:** não remover dependência por suposição; validar cada uma com busca de
imports e árvore instalada.

#### src/app/globals.css

**Ação futura:** Edit em duas PRs.

**SAN-01:** importar o CSS do package e registrar seu dist como source do
Tailwind. Manter temporariamente os tokens atuais depois do import para que eles
atuem como overrides de compatibilidade; marcar o bloco com owner e condição
explícita de remoção.

**SAN-02:** remover o bloco de compatibilidade, `@font-face` do Roboto e stacks
antigos; ativar o mapa dark compartilhado. O arquivo local conserva somente
integrações do app e estilos específicos de marca comprovados.

Essa separação faz a troca da origem dos componentes ser testada antes da
mudança visual deliberada.

#### src/app/layout.tsx

**Ação futura:** Edit em SAN-02.

**Mudança:** substituir `IBM_Plex_Sans` e `Montserrat` por Inter variável,
publicar `--font-inter` e preservar `dark`, locale, metadata e providers.

#### src/config/brand.ts

**Ação futura:** Edit em SAN-02.

**Mudança:** manter somente as decisões de marca que são realmente locais e
mapear a variante default ao vermelho coral/amarelo aprovado. Valores neutros,
status e cores categóricas passam a vir do package. A fixture de celebração
continua apenas se ainda possuir teste/consumidor real.

#### scripts/sync-brand-system.mjs e package.json

**Ação futura:** Edit em SAN-02.

**Mudança:** remover cópia de Roboto, `@fontsource/roboto` e geração de
artefatos tipográficos obsoletos. Preservar geração de SVG e tokens estritamente
locais da marca.

#### src/lib/utils.ts

**Ação futura:** Edit temporário ou Remove ao final.

**Mudança preferida:** reexportar cn de @fradelli/ui/cn durante a migração,
evitando um big-bang de imports. Remover o wrapper somente em uma mudança
separada se isso trouxer valor real.

#### src/components/ui/**

**Ação futura:** Remove somente depois que todos os imports equivalentes
apontarem para a versão instalada e os testes passarem.

**Regra:** a remoção deve preservar comportamento, markup acessível, variants e
estilos. Não combinar extração com redesign.

#### src/lib/visual-system/** e scripts/sync-visual-system.mjs

**Ação futura:** Edit/Remove gradualmente.

**Mudança:** mover apenas a geração genérica de tokens para o novo repositório.
Serializações necessárias para metadata, imagens ou assets estáticos do
Sandicts permanecem locais, mas devem consumir um snapshot público/documentado
dos tokens compartilhados em vez de manter uma segunda paleta manual.

#### Componentes que permanecem locais

- src/components/shared/brand/**;
- src/components/shared/app-shell/**;
- src/features/**;
- mapeamentos de status de API;
- routes, i18n, auth e providers;
- src/config/brand.ts e artefatos gerados de marca.

### Gates do Sandicts por PR

SAN-01 precisa provar ausência de regressão estrutural com os testes atuais e
comparação visual das telas críticas. SAN-02 aceita alteração visual, mas não
aceita alteração simultânea de copy, navegação, autorização ou chamadas de API.
Ambas executam `visual-system:check`, lint, typecheck, testes e build; SAN-02
adiciona screenshots desktop/mobile de login, shell, formulários e agendas já
existentes.

### 4. fradelli/design-system

O repositório existe e foi inicializado documentalmente pela KAN-223. Os caminhos abaixo são o contrato do scaffold técnico futuro; o conteúdo final de código deve ser produzido e validado pela KAN-222 e histórias seguintes.

| Ordem | Ação | Caminho | Responsabilidade |
| ---: | --- | --- | --- |
| 1 | Create | .nvmrc | Node 24 pinado |
| 2 | Create | .gitignore | ignorar build, cache e credenciais |
| 3 | Create | .npmrc | mapear @fradelli para GitHub Packages sem token |
| 4 | Create | package.json | scripts, exports, peers, files e publishConfig |
| 5 | Generate | package-lock.json | árvore reproduzível |
| 6 | Create | tsconfig.json | desenvolvimento estrito |
| 7 | Create | tsconfig.build.json | ESM e declarações para dist |
| 8 | Create | components.json | shadcn Radix Nova/Stone/Phosphor/RSC |
| 9 | Create | eslint.config.mjs | qualidade estática |
| 10 | Create | vitest.config.ts | testes jsdom |
| 11 | Create | src/tokens/primitives/color.json | neutros, brand e oito cores pastéis em DTCG |
| 12 | Create | src/tokens/primitives/typography.json | família Inter, pesos, tracking e line-height |
| 13 | Create | src/tokens/primitives/space.json | escala de espaço, radius, sombra e motion |
| 14 | Create | src/tokens/semantic/dark.json | aliases dark por função visual |
| 15 | Create | scripts/generate-tokens.mjs | validar DTCG e gerar CSS determinístico |
| 16 | Generate | src/styles/tokens.generated.css | custom properties; nunca editar manualmente |
| 17 | Create | src/styles/styles.css | imports, @theme, base layer e contrato público |
| 18 | Create | src/lib/cn.ts | helper interno e export público |
| 19 | Create | src/components/* | primitives importados do Sandicts |
| 20 | Create | src/**/*.test.tsx | contrato comportamental e acessível |
| 21 | Create | .storybook/main.ts | catálogo React e descoberta de stories |
| 22 | Create | .storybook/preview.ts | CSS, dark mode, viewport e backgrounds |
| 23 | Create | src/foundations/*.stories.tsx | paleta, tipografia, espaço, radius e foco |
| 24 | Create | src/components/**/*.stories.tsx | estados e variantes dos primitives |
| 25 | Create | fixtures/npm-consumer/* | instalação real do tarball com npm |
| 26 | Create | fixtures/pnpm-consumer/* | instalação real do mesmo tarball com pnpm |
| 27 | Create | scripts/build-package.mjs | build, cópia do CSS e validação do dist |
| 28 | Create | .changeset/config.json | SemVer e changelog |
| 29 | Create | .github/workflows/ci.yml | tokens, lint, tipos, testes, Storybook, build e pack |
| 30 | Create | .github/workflows/release.yml | release controlada no registry |
| 31 | Create | README.md | consumo, ownership e política de mudanças |
| 32 | Create | CHANGELOG.md | histórico público da API |

#### Contrato de package.json

O manifesto deve:

- usar name: "@fradelli/ui";
- iniciar privado no registry escolhido, sem private: true, pois esse campo
  impediria publicação;
- usar type: "module";
- publicar somente dist, README, LICENSE quando aplicável e changelog;
- expor CSS, cn e cada componente por subpath;
- declarar CSS em sideEffects;
- declarar React/React DOM como peers;
- não declarar Next.js como dependency ou peer;
- declarar publishConfig.registry para GitHub Packages;
- possuir scripts separados para format, lint, typecheck, test, build, pack e
  release, além de `tokens:generate`, `tokens:check`, `storybook` e
  `storybook:build`;
- bloquear publicação quando build, teste ou validação do tarball falhar.

#### Contrato de build

O build precisa provar:

1. ESM importável;
2. declarações TypeScript publicadas;
3. "use client" preservado em Label, Separator, Sheet e futuros primitives
   interativos;
4. CSS incluído no tarball;
5. nenhum caminho @/ interno no dist;
6. nenhum import de Next.js ou de app consumidor;
7. exports inexistentes falham antes da publicação;
8. tarball pode ser instalado por npm e pnpm.
9. Storybook estático resolve todos os exports públicos sem depender de Next.js.

#### Contrato de tokens

- JSON DTCG é a única fonte editável dos valores compartilhados;
- aliases semânticos apontam para primitives, nunca duplicam o mesmo hex;
- o gerador ordena a saída de modo estável e falha em alias inexistente;
- `tokens:check` regenera em memória/arquivo temporário e falha quando o CSS
  versionado diverge da fonte;
- cada cor de calendário possui solid, subtle, foreground e border derivados ou
  explicitamente aprovados;
- contrastes mínimos são testados contra os pares reais de foreground/background;
- nomes de token não contêm Sandicts, Kaizen, esporte, dieta ou treino;
- a primeira release suporta dark. Um mapa light somente entra quando houver
  requisito real e teste nos dois consumidores.

#### Contrato de estilos

`src/styles/styles.css` e seu CSS gerado passam a ser owner de:

- mapa dark dos tokens shadcn e extensões semânticas;
- paleta categórica de oito cores com solid, subtle, foreground e border;
- tokens success, warning, info e destructive estendidos;
- stack Inter para sans, heading e brand por meio de `--font-inter`;
- stack font-mono de sistema para conteúdo técnico;
- radius derivado de --radius;
- @theme inline do Tailwind;
- base layer de border, focus, background e foreground;
- extensões shadcn/tw-animate necessárias aos primitives publicados.

Ele não deve conter:

- geometria ou nome da marca Sandicts;
- rotas;
- classes de página;
- layout Player/Organization;
- classes de dieta, treino, reserva ou calendário de negócio;
- URLs de fonte ou assets dependentes de um app.

Os apps carregam as fontes pelo mecanismo apropriado ao framework e fornecem
as CSS variables de fonte esperadas pelo contrato.

#### Contrato do Storybook

Foundations precisa mostrar fundo, superfícies, texto, bordas, vermelho de
marca, amarelo de ação, categorias, tipografia, espaçamento, radius e foco. Cada
componente possui stories para default, hover/focus quando simulável, disabled,
loading, erro e conteúdo longo. O build do Storybook faz parte do CI, mas a
publicação pública do catálogo é opcional e não bloqueia `0.1.0`.

## Dependências e configuração

| Item | Tipo | Regra |
| --- | --- | --- |
| React / React DOM | peer + dev | mesma major suportada pelos apps; nunca embutir no bundle |
| Tailwind CSS | peer + dev | linha 4, com versão testada documentada |
| Radix | dependency | implementation detail dos primitives interativos |
| class-variance-authority | dependency | variants dos primitives |
| clsx / tailwind-merge | dependency | implementação de cn |
| Phosphor | dependency ou peer documentado | uma única política para built-in icons; props genéricas recebem ReactNode |
| shadcn CLI | dev | manutenção do source, não dependência de runtime do app |
| Storybook | dev | catálogo, documentação e render isolado dos componentes |
| Vitest / Testing Library / axe | dev | comportamento, DOM acessível e contraste |
| Changesets | dev | SemVer, changelog e release intent |
| Next.js | proibido no package | pertence aos consumidores |
| Inter | carregada pelos apps | `next/font` publica `--font-inter`; o package não envia binário nem depende de Next |
| Tokens Studio / Style Dictionary | não no início | adicionar somente se o gerador local deixar de atender web/JSON/CSS |

Todos os pins exatos pertencem ao lockfile do Design System. Peer ranges devem
cobrir apenas combinações testadas, sem exigir que Sandicts e Kaizen usem o
mesmo patch de Next.js.

Para um único package web mantido por uma pessoa, um script Node pequeno para
validar DTCG e gerar CSS é preferível a uma pipeline de tokens adicional. Essa
decisão pode ser revista se houver aplicativo nativo, múltiplos formatos de
saída ou sincronização automática com Figma.

## Estratégia de versionamento

### Antes de 1.0.0

- `0.0.0-ds.1`: prerelease opcional para validar registry e permissões;
- `0.1.0`: tokens dark, Inter contract, Storybook e primitives iniciais
  comprovados por tarball no Sandicts e Kaizen;
- `0.2.0`: componentes genéricos adicionais exigidos pelos dois produtos;
- `0.x`: mudanças incompatíveis ainda são permitidas, mas exigem changeset,
  changelog e PR coordenada nos consumidores;
- prereleases podem validar instalação sem declarar estabilidade;
- mudanças de API permanecem explícitas no changelog;
- os dois consumidores precisam passar antes de promover a API a estável.

### Depois de 1.0.0

| Mudança | Bump |
| --- | --- |
| correção sem alterar API/visual contratado | patch |
| componente ou variant retrocompatível | minor |
| remoção/rename de export ou prop | major |
| remoção/rename de token | major |
| alteração de comportamento de foco/teclado | major, salvo bug inequívoco documentado |
| mudança visual perceptível dentro do contrato atual | patch ou minor conforme alcance, sempre no changelog |

Deprecações permanecem por pelo menos uma minor estável antes da major que as
remove. Cada app atualiza por PR próprio; nenhum workflow do Design System abre
deploy direto dos consumidores.

## CI/CD e segurança

### Pull request do Design System

Executar nesta ordem:

1. format check;
2. lint;
3. typecheck;
4. testes de componente;
5. build;
6. verificação de "use client" no dist;
7. npm pack --dry-run e inspeção da allowlist;
8. instalação do tarball em fixtures temporários npm e pnpm;
9. verificação de changeset quando a mudança altera consumidores.

### Release

- publicar somente a partir da branch protegida;
- usar GitHub Environment para controlar o job de publicação;
- usar GITHUB_TOKEN quando as permissões do package/repository permitirem;
- usar token dedicado somente quando cross-repository access exigir;
- nunca versionar _authToken em .npmrc;
- manter packages publicados imutáveis;
- não usar latest para prerelease;
- criar GitHub Release e changelog para versões estáveis.

### Consumidores

- credencial de leitura fica no secret store do CI/Vercel;
- .npmrc versionado contém somente registry e variável de ambiente, nunca o
  valor;
- lockfiles são obrigatórios e instalações de CI usam modo frozen;
- Dependabot precisa de acesso explícito ao registry privado;
- falha de autenticação do registry bloqueia build; não existe fallback para
  Git URL ou cópia local.

## Procedimento de validação

### 1. Design System

Comandos finais serão definidos no package.json, mas o gate precisa cobrir:

~~~text
npm run format:check
npm run tokens:check
npm run lint
npm run typecheck
npm test
npm run build
npm run storybook:build
npm pack --dry-run
~~~

Resultado esperado:

- dist contém JS, tipos e CSS;
- o tarball contém somente a allowlist;
- cada subpath exportado resolve;
- componentes interativos preservam client boundaries;
- CSS gerado corresponde aos JSON DTCG;
- Foundations renderiza Inter, tokens dark e oito categorias;
- nenhum import proibido aparece no artefato.

### 2. Sandicts

Executar os scripts reais do repositório:

~~~text
npm run visual-system:check
npm run lint
npm run typecheck
npm run test:ci
npm run build
~~~

Acrescentar E2E/smoke e screenshots das superfícies que usam Sheet, formulários,
loading, shell e autenticação.

Resultado SAN-01: nenhuma alteração intencional de layout, cor, foco, copy ou
comportamento durante a troca da origem dos primitives.

Resultado SAN-02: a alteração visual fica restrita a Inter, superfícies dark,
vermelho/amarelo e paleta categórica; comportamento e conteúdo permanecem.

### 3. Kaizen

Depois que E04-T02 fornecer os scripts:

~~~text
pnpm format:check
pnpm lint
pnpm typecheck
pnpm test:coverage
pnpm build
~~~

Resultado esperado: o scaffold importa os estilos compartilhados, utiliza os
primitives por subpath e não recria tokens em CSS Modules.

### 4. Validação cross-package-manager

O mesmo tarball deve ser instalado em um consumidor npm e em um consumidor pnpm
com lockfiles limpos. Ambos precisam renderizar ao menos Button, Field, Input,
Alert, Skeleton e Sheet em Next.js App Router.

## Testes obrigatórios

- componente estático importado por Server Component sem hidratar o catálogo
  inteiro;
- primitive interativo importado por Server Component através do entrypoint com
  "use client";
- teclado, foco inicial e retorno de foco em Sheet/Dialog;
- disabled e aria-busy em ação pendente;
- label, description e error associados ao input;
- reduced motion no Skeleton e animações;
- mapa dark e `color-scheme: dark`;
- presença de Inter e fallback quando `--font-inter` não é fornecida;
- contraste dos pares de texto, ação, status e calendário;
- oito categorias distinguíveis por label/ícone além da cor;
- override local de marca sem alterar os defaults do outro app;
- duas versões diferentes de @fradelli/ui construindo em branches separadas
  dos consumidores;
- ausência de uma segunda cópia de React na árvore instalada.

## Critérios de aceite

- [ ] A decisão cross-product possui owner e fonte normativa.
- [ ] E04-T01 permanece como histórico concluído e E04-T02 entrega os gates antes da integração visual do Kaizen.
- [ ] A nova tarefa do Kaizen substitui explicitamente CSS Modules/tokens locais antes de E04-T04.
- [ ] @fradelli/ui é um único package sem código de domínio.
- [ ] O package não depende de Next.js nem de providers dos consumidores.
- [ ] Tokens DTCG, CSS gerado, tipos e componentes fazem parte do tarball validado.
- [ ] Inter é a família proporcional comum e não é empacotada pelo Design System.
- [ ] O mapa inicial é dark-first, com vermelho de marca, amarelo de ação e oito categorias acessíveis.
- [ ] Brand e destructive continuam semanticamente separados.
- [ ] Storybook documenta foundations, variantes e estados acessíveis.
- [ ] Client boundaries são preservadas no artefato publicado.
- [ ] React e React DOM são peers e há uma única cópia no consumidor.
- [ ] SAN-01 troca a origem dos primitives sem redesign acidental.
- [ ] SAN-02 aplica deliberadamente Inter e a nova paleta sem mudar regras de negócio.
- [ ] Kaizen adota a identidade compartilhada antes de construir o shell visual.
- [ ] npm e pnpm instalam o mesmo artefato.
- [ ] Cada app consegue permanecer em uma versão diferente do package.
- [ ] Alterações de API exigem changeset e changelog.
- [ ] Nenhum token de registry aparece no Git, logs ou documentação.
- [ ] Brand, shells, rotas, DTOs e regras de negócio continuam locais.

## Rollback

### Antes da primeira publicação

Descartar somente a branch do novo repositório. Os apps continuam usando seu
estado anterior.

### Migração do Sandicts

Manter a última versão local dos primitives em uma branch/commit recuperável.
Se a integração falhar, reverter o commit que troca imports e dependência; não
apagar ou sobrescrever histórico compartilhado.

### Kaizen

O scaffold já existe. A integração deve ser revertida como uma PR isolada,
restaurando temporariamente os CSS Modules já versionados. Depois da primeira
versão saudável, rollback significa fixar a versão anterior de @fradelli/ui,
não criar um segundo sistema visual permanente.

### Release com defeito

Nunca sobrescrever uma versão publicada. Publicar patch corretivo ou reverter o
consumidor para a versão anterior. Breaking change exige nova major.

## Riscos e controles

| Risco | Controle |
| --- | --- |
| @fradelli/ui virar common | allowlist de responsabilidades e revisão de imports proibidos |
| Tailwind não detectar classes do package | @source explícito e teste do tarball real |
| bundler remover "use client" | build configurado e gate que inspeciona dist |
| duas cópias de React | peer dependency e verificação da árvore instalada |
| Kaizen divergir visualmente | consumir os mesmos tokens, não copiar valores |
| app precisar customização | tokens, className e composição; sem flags de produto |
| Design System bloquear deploy dos apps | lockfile e upgrades por PR independente |
| package privado quebrar CI | permissions/secret documentados e smoke de instalação |
| abstração prematura de agenda | extrair apenas primitives de data após dois usos reais |
| atualização shadcn apagar customizações | comparar e revisar upstream; nunca regenerar cegamente |
| vermelho de marca parecer erro | destructive separado e sempre acompanhado de semântica textual/ícone |
| calendário depender só de cor | label, ícone/padrão e contraste testados além da tonalidade |
| carregamento duplicado de Inter | cada app carrega uma vez no root; package referencia somente a variável CSS |

## Decisões confirmadas para a primeira tarefa de código

1. O repositório é `fradelli/design-system`.
2. O package e scope permanentes são `@fradelli/ui`.
3. O registry é GitHub Packages e o package começa privado.
4. Radix Nova permanece como base comum do shadcn/ui.
5. Enquanto houver um único desenvolvedor, o owner nominal de breaking changes é `frontend/design-system`.
6. O repositório permanece sem `LICENSE` por enquanto; uma distribuição pública exige decisão explícita de licença.

Dark-first, Inter e a paleta registrada neste documento também estão aprovados. Credenciais nunca entram no repositório; os pins serão registrados pelo lockfile criado na KAN-222.

## Fontes oficiais

- [shadcn/ui: princípios e distribuição](https://ui.shadcn.com/docs)
- [shadcn/ui: configuração para UI compartilhada](https://ui.shadcn.com/docs/monorepo)
- [shadcn/ui: CLI e CSS compartilhado](https://ui.shadcn.com/docs/cli)
- [Tailwind CSS: detectar classes em bibliotecas externas](https://tailwindcss.com/docs/detecting-classes-in-source-files)
- [Figma: variables, collections, modes e aliases](https://help.figma.com/hc/en-us/articles/14506821864087-Overview-of-variables-collections-and-modes)
- [Figma: importar tokens DTCG](https://help.figma.com/hc/en-us/articles/15343816063383-Modes-for-variables)
- [Storybook: catálogo e testes de componentes](https://storybook.js.org/docs/writing-tests)
- [Next.js: Server e Client Components para autores de bibliotecas](https://nextjs.org/docs/app/getting-started/server-and-client-components)
- [React: risco de React duplicado](https://react.dev/warnings/invalid-hook-call-warning)
- [Changesets: versionamento e changelog](https://github.com/changesets/changesets)
- [GitHub Packages: npm registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-npm-registry)
- [Semantic Versioning 2.0.0](https://semver.org/)
