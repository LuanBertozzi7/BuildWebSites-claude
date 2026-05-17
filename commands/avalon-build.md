# Avalon Build

Você é o orquestrador principal do sistema Avalon. Quando este comando for executado, siga o pipeline abaixo na ordem exata.

## Agentes disponíveis
- `prompt-engineer` — refina e aprimora os prompts enviados aos agentes antes de cada etapa
- `backend-architect` — planejamento de arquitetura, contratos de API, schema do banco
- `backend-developer` — implementação do backend (NestJS/Fastify, migrations, auth, testes)
- `fullstack-developer` — implementação full-stack coesa quando back e front compartilham tipos (Next.js + tRPC)
- `frontend-developer` — especialista em frontend (React/Vue/Angular), use quando o back já existir ou for separado
- `ui-ux-designer` — sistema de design, paleta, tipografia, design tokens, acessibilidade
- `documentation-expert` — documentação final do projeto (README, API docs, NEXT_STEPS)

## Pipeline

### Etapa 0 — Refinar prompts (prompt-engineer)

Antes de acionar qualquer agente especialista (etapas 3, 4, 5 e 6), passe o prompt planejado pelo agente `prompt-engineer`.

O prompt-engineer vai analisar cada instrução e devolver uma versão mais precisa, estruturada e eficaz. Use o output refinado como o prompt real enviado ao agente alvo.

Faça isso para cada agente individualmente, com o contexto específico daquela etapa.

### Etapa 1 — Ler e aprimorar a ideia

Leia o arquivo `C:\Users\LuanB\Desktop\readme.md`.

Com base no que foi preenchido, faça o seguinte:
- Preencha seções vazias ou rasas com sugestões baseadas no contexto da ideia
- Expanda os requisitos funcionais (adicione os que faltam mas fazem sentido)
- Sugira stack técnica se não especificada (padrão: NestJS/Node.js no back, Next.js no front, PostgreSQL como banco)
- Melhore a descrição da ideia, objetivo e público-alvo se estiverem vagos
- Adicione uma seção `## Arquitetura Sugerida` com: estrutura de pastas, entidades principais, rotas principais da API
- Crie um arquivo `project-context.json` na pasta do projeto com todas as decisões de arquitetura, stack, requisitos e design tokens — esse arquivo é o canal de comunicação entre os agentes

Reescreva o arquivo `C:\Users\LuanB\Desktop\readme.md` com a versão aprimorada.

Mostre ao usuário o documento final antes de continuar. Peça confirmação para prosseguir.

### Etapa 2 — Criar estrutura do projeto

Pergunte ao usuário onde criar o projeto (ou use `C:\Users\LuanB\projetos\[nome-do-projeto]` como padrão).

Crie a estrutura de pastas conforme a stack definida. Inicialize os projetos (package.json, configs base, gitignore, etc). Salve o `project-context.json` na raiz.

### Etapa 3 — Arquitetura (backend-architect)

Acione o agente `backend-architect` com o conteúdo completo do `project-context.json`.

Ele deve entregar:
- Diagrama de arquitetura (Mermaid)
- Contratos de API (OpenAPI 3.1 spec)
- Schema do banco com relacionamentos e índices
- Estratégia de autenticação e segurança

Salve o output no `project-context.json` antes de continuar.

### Etapa 4 — Design (ui-ux-designer)

Acione o agente `ui-ux-designer` com o `project-context.json`.

Ele deve entregar:
- Sistema de design completo (paleta, tipografia, espaçamentos)
- Design tokens em CSS variables
- Layout das telas principais
- Diretrizes de acessibilidade

Salve o output no `project-context.json` antes de continuar.

### Etapa 5 — Implementação

Escolha o modo de implementação baseado na stack definida no project-context.json:

**Modo A — Stack separada (NestJS + Next.js ou similar):**
Acione em paralelo:
- **`backend-developer`**: lê o project-context.json, implementa migrations, services, controllers, auth e testes seguindo o OpenAPI spec do architect
- **`frontend-developer`**: lê o project-context.json, implementa páginas, componentes e integração com a API usando os design tokens do ui-ux-designer

**Modo B — Stack coesa (Next.js + tRPC + Drizzle):**
Acione apenas:
- **`fullstack-developer`**: lê o project-context.json completo, implementa banco, API e frontend com tipos compartilhados e design tokens integrados

### Etapa 6 — Documentação (documentation-expert)

Após a implementação finalizar, acione o agente `documentation-expert` com o `project-context.json` e o código gerado.

Ele deve entregar:
- `README.md` completo na raiz do projeto
- Documentação da API a partir do OpenAPI spec
- Guia de setup e configuração local
- `NEXT_STEPS.md` com o que falta fazer manualmente (env vars, deploy, banco, etc.)

### Etapa 7 — Consolidação final

Resolva conflitos entre os outputs dos agentes, garanta que rotas da API batem com as chamadas do frontend, e confirme ao usuário que o projeto está pronto.
