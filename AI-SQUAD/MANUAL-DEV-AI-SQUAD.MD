---
titulo: Boas Práticas de Desenvolvimento — Celcoin
categoria: AI-SQUAD
confidencialidade: publico
tags: [n8n, automação, boas-praticas, workflows, desenvolvimento , padrões, fluxos, checklist]
---


# Boas Práticas de Desenvolvimento — Celcoin


## 1. Os dois tipos de projeto

Na Celcoin trabalhamos com dois modelos de desenvolvimento diferentes, e as boas práticas mudam um pouco entre eles:

- **Projetos de codebase**: repositórios de código tradicionais, como o **cel_bot** e o **Gira**, além dos workflows de automação no **N8N**. Aqui o time programa diretamente (com ou sem apoio de agentes de IA).
- **Projetos Lovable**: aplicações construídas na plataforma Lovable (ex.: **KYP Vision**, **Brain**), onde um agente de IA da própria Lovable escreve o código a partir de prompts.

As seções abaixo detalham as práticas recomendadas para cada caso.

---

## 2. Projetos de codebase (cel_bot, Gira, N8N)

### 2.1 Jira — toda tarefa precisa de card

- Antes de começar a desenvolver, a tarefa precisa existir como card no Jira (nada de código "solto" sem rastreabilidade).
- O código do card (ex.: `AA-123`) é o que amarra a tarefa à branch, ao PR e ao commit — é assim que o time consegue rastrear o que foi feito e por quê.

### 2.2 Git Flow — branches e Pull Requests

- Nunca commitar direto na `main`/`master`. Toda mudança entra por branch + Pull Request.
- Convenção de branches: `feature/AA-XXX-nome-da-tarefa`, `hotfix/AA-XXX-nome-do-fix`, `release/versao` (seguindo o modelo Git Flow) — sempre com o código do card do Jira (`AA-XXX`) no início do nome, seguido de uma descrição curta da tarefa.
- Todo PR precisa de:
  - descrição clara do que foi feito e por quê;
  - vínculo com o card correspondente no Jira (o número `AA-XXX` da branch já ajuda a rastrear isso);
  - pelo menos uma revisão (code review) antes do merge.
- Mensagens de commit devem ser objetivas e, sempre que possível, seguir um padrão (ex.: Conventional Commits — `feat:`, `fix:`, `chore:` etc.), facilitando o histórico e a geração de changelogs.

### 2.3 Ferramentas e skills de apoio

- **Context7 (MCP)**: usar para consultar a documentação atualizada de bibliotecas e frameworks direto durante o desenvolvimento. Evita que o agente de IA "alucine" APIs desatualizadas ou inexistentes — importante sempre que estiver integrando uma lib nova ou com dúvida de sintaxe/versão.
- **Impeccable (skill de design)**: skill voltada para deixar a UI/frontend gerada por agentes de IA com um acabamento mais profissional e consistente. Usar quando o agente estiver construindo ou ajustando telas em projetos de codebase (fora do Lovable).
- **Skill de Design System da Celcoin**: aplica o padrão visual oficial usado nos nossos produtos (fonte Inter, roxo primário `#7664FA`, componentes shadcn/ui, densidade compacta `text-xs`) — o mesmo padrão já usado no KYP Vision e no Gira. Sempre que for criar ou alterar telas, usar essa skill para manter a identidade visual consistente entre os produtos.

### 2.4 Segurança — API keys e credenciais

- Nunca expor chaves de API, tokens, senhas ou strings de conexão diretamente no código, em arquivos de configuração versionados, ou em qualquer branch (mesmo temporária).
- Usar sempre variáveis de ambiente / gerenciador de segredos. Se encontrar uma credencial hardcoded em código existente, reportar para rotação imediata em vez de reutilizá-la.
- **N8N**: a mesma regra vale para workflows — nunca expor API keys direto nos nodes. Utilizar sempre as conexões/credenciais já cadastradas no workspace do N8N, em vez de colar chaves manualmente nos nodes.

### 2.5 GitHub — conta e identidade

- Criar conta no GitHub usando o e-mail corporativo (`@celcoin.com.br`). Não usar contas pessoais para trabalho da Celcoin.
- Padrão de nome de usuário: `@seunome-celcoin` (ex.: `@carlos-celcoin`).

---

## 3. Projetos Lovable (KYP Vision, Brain)

> **Ponto de atenção**: nem todo projeto Lovable é mantido por nós depois de pronto. Muitas vezes quem pediu a demanda e fica responsável pela manutenção é outro time, não-técnico. Nós não somos um time de sustentação — não vamos estar sempre por perto para corrigir problema de login solto, tela de usuário reinventada ou domínio mal configurado depois que o projeto sai das nossas mãos. Por isso esse checklist é obrigatório e não opcional: é o que garante que o projeto continue seguro e consistente mesmo sendo mantido por quem não tem bagagem técnica.

### 3.1 Todo projeto novo precisa ter (checklist obrigatório)

Esse checklist vale para **ferramentas internas** — qualquer projeto que tenha login, dados de uso interno ou seja usado por colaboradores (o padrão do KYP Vision, Skills Catalog, Gira etc.). Para **sites/páginas públicas** (institucional, landing page, demo de vendas, portal de documentação), que não têm login nem usuário interno, esse checklist não se aplica — usar o template B da seção 5.2 nesses casos.

Para ferramentas internas, todo projeto novo criado no Lovable precisa sair com os seguintes itens configurados, sem exceção:

- **SSO Celcoin**: login **apenas** via Google SSO, restrito a contas `@celcoin.com.br`. Nada de login por e-mail/senha em paralelo — é só Google, sem exceção.
- **Design System da Celcoin**: aplicar o padrão visual oficial (fonte Inter, roxo primário `#7664FA`, componentes shadcn/ui, densidade compacta `text-xs`) desde o início do projeto. Referência oficial no Claude Design: https://claude.ai/design/p/d8691d6d-1378-4530-92ce-b0c7fbbe25d3?via=share
- **Tela de gerenciamento e convite de usuários**: padrão de papéis **criador / aprovador / admin**, igual aos demais projetos — não reinventar esse fluxo a cada projeto novo.
- **SendGrid configurado**: chaves do SendGrid configuradas para o envio de e-mails (convites, notificações etc.).
- **Cloudflare e domínio**: configuração de Cloudflare e domínio próprio do projeto.

Para o item de Cloudflare/domínio, pedir para o **Gabriel Monteiro** ou para o **Meirinho** fazerem essa configuração — não configurar por conta própria.

### 3.2 Usar o Claude conectado ao Lovable

Em vez de digitar prompts direto no chat do Lovable, o recomendado é usar o **Claude conectado ao Lovable** (via conector/MCP) para montar e enviar os prompts. Isso ajuda porque o Claude consegue:

- ler o contexto do projeto (arquivos, conhecimento salvo) antes de escrever o prompt;
- discutir e planejar a abordagem antes de mandar qualquer mudança para o agente do Lovable (modo de planejamento), evitando prompts genéricos que geram retrabalho;
- enviar mensagens mais estruturadas e ricas em contexto para o agente do Lovable, em vez de instruções soltas;
- revisar o diff gerado antes de aceitar a mudança.

Na prática, isso reduz o número de iterações (e créditos) gastos no Lovable, porque o prompt já chega mais completo e direcionado.

### 3.3 Escolha do modelo/agente

- Usar **Claude Sonnet 5 (esforço médio)** como padrão para o dia a dia — é suficiente para a grande maioria das tarefas de desenvolvimento.
- Reservar agentes mais avançados (esforço mais alto, orquestração com múltiplos agentes) apenas para casos de **extrema necessidade**: tarefas muito complexas, investigações profundas ou quando o Sonnet 5 médio não for suficiente para resolver o problema.

---

## 4. Regra geral de uso de IA

- Usar apenas as ferramentas de IA fornecidas/homologadas pela empresa (Claude, Cursor, as API keys fornecidas pela empresa e os conectores oficiais).
- **Não usar IA de uso pessoal** (contas pessoais de ChatGPT, Claude, etc.) para trabalho da Celcoin — por segurança da informação e confidencialidade dos dados de clientes, parceiros e da própria empresa.

---

## 5. Templates de contexto para iniciar um projeto novo

Usar o template abaixo como primeira mensagem para o agente — seja direto no Lovable, seja pelo Claude conectado ao Lovable (ver seção 3.2). Preencher os campos entre `<>` e colar antes de pedir qualquer implementação. Tem dois templates: **A** para ferramenta interna (com login e banco de dados) e **B** para site/página pública (sem login). Usar o que corresponde ao tipo de projeto (ver seção 3.1).

### 5.1 Template A — Ferramenta interna (Lovable + Supabase)

Cobre boa parte do checklist obrigatório da seção 3.1 em nível de código (design system, SSO, RLS). SendGrid e Cloudflare/domínio continuam sendo configuração manual — não esquecer de fazer depois (ou pedir para o Gabriel Monteiro/Meirinho, no caso do domínio).

```
# Contexto do projeto
Nome: <NOME_DO_PROJETO>
Objetivo: <o que o projeto resolve, em 2-3 frases>
Card no Jira: <AA-XXX>
Usuários: <quem usa, com que nível de acesso>
Sucesso é: <critério objetivo de "pronto">
Fora de escopo nesta fase: <o que NÃO construir agora>

# Stack e restrições
- Frontend: Lovable (React + Tailwind + shadcn/ui), seguindo a skill de Design System da Celcoin (Inter, roxo #7664FA, densidade compacta text-xs)
- Backend: Supabase (Postgres + Auth + Edge Functions)
- Login: SSO Celcoin — Google OAuth restrito a @celcoin.com.br, sem login por e-mail/senha em paralelo
- Gestão de usuários: papéis criador / aprovador / admin, com tela de convite/gerenciamento igual à dos outros projetos internos
- Antes de escrever código, me apresente um plano: modelo de dados, rotas/telas, políticas de acesso. Só implemente depois que eu aprovar.
- Construa uma feature por vez. Ao final de cada uma, liste o que devo validar no preview antes de seguir.

# Segurança — regras não negociáveis
1. RLS habilitado em TODAS as tabelas no mesmo migration em que a tabela é criada. Nunca deixe tabela sem policy "para configurar depois".
2. Políticas fail-closed: nega por padrão, libera explicitamente. Escreva policies separadas para SELECT, INSERT, UPDATE e DELETE — não presuma que restringir SELECT protege UPDATE/DELETE.
3. Segredos (tokens, API keys, service role) vivem apenas no gerenciador de secrets. Nunca no código, nunca em variáveis do frontend, nunca em texto no chat (nem com o Lovable, nem com o Claude). Use placeholders: <TOKEN>, <CLIENT_ID>, <ENDPOINT>.
4. Service role key só em Edge Function, nunca exposta ao cliente.
5. Toda chamada a serviço externo (incluindo SendGrid) passa por Edge Function, não pelo browser.

# Modelagem de dados
- Prefixe tabelas por domínio: <prefixo>_<entidade>.
- Campos de auditoria em toda tabela: created_at, updated_at, created_by.
- Estados críticos (visibilidade, status, papel) protegidos por CHECK constraint no banco, não só por validação no frontend.
- Defaults seguros: o estado inicial de qualquer registro deve ser o mais restritivo possível.
- Migrations idempotentes (IF NOT EXISTS / DROP POLICY IF EXISTS) e nomeadas de forma descritiva.

# Ingestão de conteúdo externo (se aplicável)
- Todo conteúdo importado entra como rascunho privado e não classificado. Só vira conteúdo compartilhado após classificação humana explícita.
- Integrações externas operam por allowlist: eu defino quais espaços, projetos ou pastas podem ser lidos. Nada fora da lista.
- Toda fonte de ingestão escreve através de um único módulo compartilhado com as validações. Não duplique lógica de escrita por conector.

# Ambiente colaborativo
- Há mais de uma pessoa editando este projeto.
- Antes de refazer qualquer ação que pareceu falhar, VERIFIQUE o estado real: consulte o banco, leia o arquivo. Chamadas podem ter concluído mesmo sem retorno visível. Nunca reexecute um migration às cegas.
- Antes de criar tabela, coluna ou arquivo, cheque se já existe.

# Convenções
- TypeScript estrito, sem `any`.
- Tipos do Supabase gerados, não escritos à mão.
- Componentes pequenos e um design system consistente desde o início.
- Nada de dados reais em seeds, exemplos ou fixtures — use dados fictícios.

# Primeiro passo
Não escreva código ainda. Me devolva:
1. O modelo de dados proposto (tabelas, colunas, relações)
2. As políticas de RLS por tabela
3. A lista de telas e rotas
4. As perguntas que você tem sobre o escopo
```

### 5.2 Template B — Site/página pública (sem login)

Para institucional, landing page, portal de documentação ou demo de vendas — sem usuário logado, sem RLS, sem gestão de papéis. O ponto de atenção aqui é outro: design system, performance e domínio/publicação corretos.

```
# Contexto do projeto
Nome: <NOME_DO_PROJETO>
Objetivo: <o que o projeto resolve/comunica, em 2-3 frases>
Card no Jira: <AA-XXX>
Público: <quem visita a página — externo, prospect, cliente etc.>
Sucesso é: <critério objetivo de "pronto">
Fora de escopo nesta fase: <o que NÃO construir agora>

# Stack e restrições
- Frontend: Lovable (React + Tailwind + shadcn/ui), seguindo a skill de Design System da Celcoin (Inter, roxo #7664FA)
- Sem login/autenticação — página pública. Não criar tabelas de usuário nem RLS desnecessariamente.
- Antes de escrever código, me apresente um plano: seções/páginas, componentes, e onde entra qualquer formulário (lead, contato etc.). Só implemente depois que eu aprovar.
- Construa uma seção por vez. Ao final de cada uma, liste o que devo validar no preview antes de seguir.

# Segurança
- Se houver formulário (captura de lead, contato), a submissão passa por Edge Function — nunca por chave de API exposta no frontend.
- Segredos (tokens, API keys) vivem apenas no gerenciador de secrets. Nunca no código, nunca em texto no chat. Use placeholders: <TOKEN>, <CLIENT_ID>, <ENDPOINT>.

# Qualidade e publicação
- Responsivo mobile-first, com atenção a performance (imagens otimizadas, sem libs pesadas desnecessárias).
- Nada de dados reais em exemplos/mockups — usar dados fictícios até o conteúdo real ser aprovado.
- Domínio e Cloudflare: pedir para o Gabriel Monteiro ou o Meirinho configurarem antes da publicação final — não configurar por conta própria.

# Primeiro passo
Não escreva código ainda. Me devolva:
1. A estrutura de seções/páginas proposta
2. A lista de componentes reutilizáveis
3. As perguntas que você tem sobre o escopo/conteúdo
```

---

*Última atualização: 22/09/2026. Dúvidas ou sugestões de ajuste, falar com o time de desenvolvimento.*
