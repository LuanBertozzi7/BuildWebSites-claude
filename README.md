# Avalon Build — Sistema de Agentes Claude para Construção de Sites

Pipeline de construção de projetos web usando agentes especializados no Claude Code. Você preenche um template com sua ideia, roda `/avalon-build`, e o sistema cuida do resto: arquitetura, design, implementação e documentação.

## Como usar

**1. Preencha o template no Desktop (`readme.md`):**

```markdown
# [Nome do Projeto]
## Ideia
## Objetivo
## Contexto
## Repositório
## Stack (opcional)
## Requisitos Funcionais
## Requisitos Não Funcionais
## Paleta de cores (opcional)
```

**2. Rode o comando:**

```
/avalon-build
```

Pronto. O orquestrador lê sua ideia, expande o documento, confirma com você, e dispara os agentes em sequência.

---

## Pipeline completo

```
Etapa 0 — prompt-engineer
          Refina o prompt de cada agente antes de chamar

Etapa 1 — Orquestrador
          Lê Desktop/readme.md, expande seções vazias,
          cria project-context.json → pede confirmação

Etapa 2 — Orquestrador
          Cria estrutura de pastas e inicializa o projeto

Etapa 3 — backend-architect
          Diagrama Mermaid, OpenAPI 3.1 spec,
          schema do banco, estratégia de auth
          → salva no project-context.json

Etapa 4 — ui-ux-designer
          Paleta, tipografia, design tokens CSS,
          layout das telas, diretrizes de acessibilidade
          → salva no project-context.json

Etapa 5 — Implementação
          Modo A │ backend-developer + frontend-developer (paralelo)
          Modo B │ fullstack-developer (Next.js + tRPC, stack coesa)

Etapa 6 — documentation-expert
          README.md, API docs, guia de setup, NEXT_STEPS.md

Etapa 7 — Consolidação
          Resolve conflitos, valida rotas x chamadas do front
```

O `project-context.json` na raiz do projeto é o backbone do sistema — cada agente lê e escreve nele, passando contexto pra frente sem precisar de um orquestrador dedicado rodando.

---

## Agentes

| Agente | Categoria | Papel |
|--------|-----------|-------|
| `prompt-engineer` | ai-specialists | Refina prompts antes de cada etapa |
| `backend-architect` | development-team | Arquitetura, OpenAPI spec, schema do banco |
| `backend-developer` | custom | Implementação backend (NestJS/Fastify + Prisma + JWT) |
| `fullstack-developer` | development-team | Implementação full-stack coesa (Next.js + tRPC + Drizzle) |
| `frontend-developer` | development-team | Frontend separado (React/Vue/Angular) |
| `ui-ux-designer` | development-team | Sistema de design, tokens, acessibilidade (WCAG 2.2) |
| `documentation-expert` | expert-advisors | README, API docs, NEXT_STEPS |

### Modo A vs Modo B

**Modo A — Stack separada (NestJS + Next.js ou similar)**  
`backend-developer` + `frontend-developer` rodam em paralelo. Bom quando back e front têm repos separados ou stacks diferentes.

**Modo B — Stack coesa (Next.js + tRPC + Drizzle)**  
Só o `fullstack-developer`. Tipos compartilhados entre back e front, zero duplicação de schemas.

---

## Comandos disponíveis

### `/avalon-build`
Pipeline completo: lê o readme, expande a ideia, cria a estrutura, chama os agentes em sequência, documenta o projeto.

### `/security-audit`
Auditoria de segurança sob demanda. Roda no projeto atual e cobre:
- OWASP Top 10 completo (arquivo e linha específicos)
- Análise de dependências com CVEs
- Hardcoded secrets e variáveis de ambiente
- Headers de segurança
- Auth e gestão de sessão

Gera `SECURITY_AUDIT.md` com vulnerabilidades por severidade + checklist de remediação.

---

## Instalação

### Agentes via npx

```bash
npx claude-code-templates@latest --agent development-team/frontend-developer
npx claude-code-templates@latest --agent development-team/ui-ux-designer
npx claude-code-templates@latest --agent development-team/backend-architect
npx claude-code-templates@latest --agent development-team/fullstack-developer
npx claude-code-templates@latest --agent expert-advisors/documentation-expert
npx claude-code-templates@latest --agent ai-specialists/prompt-engineer
```

### Agentes customizados (copiar manualmente para `~/.claude/agents/`)

- `backend-developer.md` — implementação backend com OWASP integrado
- `security-engineer.md` — usado pelo `/security-audit`

### Comandos (copiar para `~/.claude/commands/`)

- `avalon-build.md` — pipeline principal
- `security-audit.md` — auditoria sob demanda

---

## Stack padrão

Quando não especificada no readme, o orquestrador usa:

| Camada | Default |
|--------|---------|
| Backend | NestJS + TypeScript strict |
| ORM | Prisma |
| Banco | PostgreSQL |
| Auth | JWT + bcrypt (12 rounds) |
| Frontend | Next.js 15 + App Router |
| CSS | Tailwind v4 |
| Testes | Jest + Supertest / Vitest |

---

## Estrutura do projeto gerado

```
[nome-do-projeto]/
├── project-context.json   ← backbone: decisões e contratos entre agentes
├── NEXT_STEPS.md          ← o que falta fazer manualmente
├── README.md              ← gerado pelo documentation-expert
├── SECURITY_AUDIT.md      ← gerado pelo /security-audit (se rodar)
├── backend/               ← Modo A: NestJS
│   ├── src/
│   ├── prisma/
│   └── .env.example
└── frontend/              ← Modo A: Next.js
    ├── src/
    └── .env.example
```

---

## Filosofia

- **Nenhum agente depende de outro rodando** — o `project-context.json` é o estado compartilhado
- **prompt-engineer refina antes de cada chamada** — agentes recebem prompts cirúrgicos, não genéricos
- **Segurança integrada no backend-developer** — OWASP, bcrypt, JWT rotation, audit log são padrão, não opcional
- **UI/UX primeiro no design** — design tokens chegam ao frontend antes da implementação começar
