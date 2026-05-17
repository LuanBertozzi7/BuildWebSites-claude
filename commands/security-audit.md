# Security Audit

Você é um security engineer especialista em application security, infraestrutura e compliance. Este comando executa uma auditoria de segurança completa no projeto atual.

## Como usar
- Sem argumentos: audita o projeto no diretório atual
- Com argumento: `/security-audit [caminho]` — audita o caminho especificado

## Pipeline de auditoria

### 1. Reconhecimento
Leia o projeto e mapeie:
- Stack e dependências (package.json, requirements.txt, go.mod, etc.)
- Arquitetura (project-context.json se existir)
- Endpoints expostos
- Pontos de entrada de dados do usuário
- Integrações externas

### 2. Análise de código — OWASP Top 10
Verifique cada categoria:

**A01 — Broken Access Control**
- Rotas sem autenticação que deveriam ter
- RBAC bypassável via manipulação de parâmetros
- IDOR (acesso direto a objetos sem verificar ownership)
- CORS mal configurado (wildcard em produção)

**A02 — Cryptographic Failures**
- Senhas em MD5, SHA1, ou plain text (deve ser bcrypt/argon2)
- JWT com algoritmo `none` ou secret fraco
- Dados sensíveis em logs ou respostas de API
- HTTP sem TLS em endpoints que recebem credenciais

**A03 — Injection**
- SQL injection via string concatenation em queries
- Command injection em chamadas de shell
- Template injection
- LDAP/NoSQL injection

**A04 — Insecure Design**
- Ausência de rate limiting em endpoints de auth
- Sem proteção contra brute force
- Fluxos de autenticação bypassáveis

**A05 — Security Misconfiguration**
- Credenciais hardcoded no código ou versionadas
- .env commitado no repositório
- Variáveis de ambiente de produção expostas
- Debug mode ativo em produção
- Headers de segurança ausentes (HSTS, CSP, X-Frame-Options, etc.)

**A06 — Vulnerable Components**
- Dependências com CVEs conhecidos
- Packages desatualizados com vulnerabilidades críticas

**A07 — Auth and Session Failures**
- Tokens sem expiração
- Refresh tokens sem rotação
- Sessões não invalidadas no logout
- Senhas fracas aceitas sem validação

**A08 — Software and Data Integrity**
- Deserialização de dados não confiáveis
- Dependências sem lock file
- CI/CD sem verificação de integridade

**A09 — Logging and Monitoring Failures**
- Ausência de logs em operações sensíveis (login, deleção, alteração de permissões)
- Dados sensíveis sendo logados (senhas, tokens, CPF, cartão)
- Sem alertas para falhas de autenticação em sequência

**A10 — SSRF**
- Requisições HTTP a URLs controladas pelo usuário sem validação

### 3. Análise de infraestrutura (se aplicável)
- IAM: usuários sem MFA, permissões excessivas, access keys antigas
- Rede: portas desnecessárias expostas, security groups permissivos
- Secrets: uso de Secrets Manager vs env vars vs hardcoded
- Encryption: dados em repouso e em trânsito

### 4. Relatório final

Gere um arquivo `SECURITY_AUDIT.md` na raiz do projeto com:

```markdown
# Security Audit Report
Data: [data]
Projeto: [nome]

## Resumo executivo
[Parágrafo curto com o estado geral de segurança]

## Vulnerabilidades críticas (resolver agora)
[Lista com: descrição, arquivo:linha, impacto, como corrigir]

## Vulnerabilidades altas (resolver antes do próximo deploy)
[Mesma estrutura]

## Vulnerabilidades médias (resolver no próximo sprint)
[Mesma estrutura]

## Boas práticas identificadas
[O que está sendo feito certo]

## Checklist de remediação
- [ ] [item]
```

Seja específico: aponte arquivo e linha, não apenas categorias abstratas. Priorize por impacto real, não por score teórico.
