# Influenciadores CRM — Plataforma de Gestão de Influenciadores

Plataforma Next.js 15 com React, TypeScript, Prisma ORM e PostgreSQL para gerenciar influenciadores, campanhas, publicações e métricas de redes sociais.

## 📋 Pré-requisitos

- **Node.js 20+** (LTS recomendado)
- **npm 10+**
- **PostgreSQL 14+** (local ou via Docker)
- **Redis** (para rate limiting e BullMQ, usado a partir de Phase 4)
- **.env** configurado com variáveis de ambiente

## 🚀 Quick Start

### 1. Clonar repositório e instalar dependências

```bash
git clone <repo-url> influenciadores-app
cd influenciadores-app
npm install
```

### 2. Configurar variáveis de ambiente

```bash
cp .env.example .env
# Edite .env com os valores reais:
# - NODE_ENV=development
# - DATABASE_URL=postgresql://user:password@localhost:5432/influenciadores_dev
# - NEXTAUTH_SECRET (gere com: openssl rand -base64 32)
# - NEXTAUTH_URL=http://localhost:3000
```

### 3. Configurar banco de dados

```bash
# Criar e aplicar schema no PostgreSQL
npm run db:migrate:dev

# (Opcional) Popular com dados de teste
npm run db:seed
```

### 4. Iniciar servidor de desenvolvimento

```bash
npm run dev
```

Acesse [http://localhost:3000](http://localhost:3000) no navegador.

## 📁 Estrutura do Projeto

Ver [`PROJECT_STRUCTURE.md`](./PROJECT_STRUCTURE.md) para detalhes completos.

Pastas principais:
- **`src/app/`** — Next.js 15 App Router (páginas, layouts, API routes)
- **`src/components/`** — React components reutilizáveis (shadcn/ui)
- **`src/actions/`** — Server Actions (mutações seguras no servidor)
- **`src/lib/`** — Utilities: auth, database, encryption, validators, permissions
- **`src/types/`** — TypeScript types e interfaces
- **`prisma/`** — Schema Prisma e migrations

## 🛠️ Scripts Úteis

```bash
# Desenvolvimento
npm run dev              # Iniciar servidor dev

# Linting & Formatação
npm run lint            # Verificar linting
npm run lint:fix        # Corrigir problemas de linting
npm run format          # Formatar código com Prettier
npm run type-check      # Verificar tipos TypeScript

# Banco de Dados
npm run db:generate     # Gerar Prisma client
npm run db:migrate:dev  # Criar e aplicar migration
npm run db:seed         # Popular dados de teste
npm run db:studio       # Abrir Prisma Studio (UI para DB)
npm run db:reset        # Resetar DB completamente (cuidado!)

# Build & Produção
npm run build           # Build para produção
npm start               # Iniciar servidor de produção

# Testes (configurados para Phase 0.2+)
npm test               # Rodar todos os testes
npm run test:watch     # Modo watch
```

## 🔐 Segurança & Configuração

### Autenticação (Auth.js v5)

- **Strategy**: Sessions (JWT + cookies)
- **Config**: `src/lib/auth.ts`
- **Provedor**: Nextauth.js com Prisma adapter

Fluxos suportados:
- ✅ Email/Password (Phase 0)
- ⏳ OAuth Social (Phase 5: Instagram, TikTok, YouTube, Facebook)

### Criptografia de OAuth Tokens

Tokens de redes sociais são criptografados com **AES-256-GCM** antes de salvar no DB:

```typescript
// src/lib/encryption.ts
const encrypted = encryptToken(accessToken, process.env.OAUTH_TOKEN_ENCRYPTION_KEY);
// Salvar `encrypted` no DB
```

### RBAC (Role-Based Access Control)

- **Roles**: ADMIN, MANAGER, ANALYST, VIEWER
- **Granular permissions**: View, Create, Edit, Delete por recurso
- **Verificação**: Middleware + Server Actions
- **Auditoria**: Todos as ações logadas em `AuditLog`

## 📊 Fases de Desenvolvimento

| Fase | Escopo | Status |
|------|--------|--------|
| **0** | Setup (schema, auth, CRUD influenciador/campanha) | 🔄 Em andamento |
| **1** | Influencer CRUD completo + soft delete | ⏳ Próximo |
| **2** | Campaign CRUD + influencer assignment | ⏳ Próximo |
| **3** | File storage (S3/R2) + media kit upload | ⏳ Futuro |
| **4** | Publication tracking (manual) | ⏳ Futuro |
| **5** | OAuth social (Instagram, TikTok, Facebook, YouTube) | ⏳ Futuro |
| **6** | Reports, exports, LGPD compliance | ⏳ Futuro |

## 🗄️ Banco de Dados

**Schema Prisma** com 25 tabelas:

```
Organização (multi-tenant)
├── Users (RBAC + permissions)
├── Influencers (prospect → active)
│   ├── SocialAccounts (Instagram, TikTok, YouTube, Facebook)
│   │   └── SocialMetricSnapshots (histórico de métricas)
│   │       └── AudienceData (localização, idade, gênero, interesse)
│   ├── ProfessionalProfile (taxas, categorias)
│   ├── CommercialProfile (termos, preferências)
│   └── Addresses (endereço)
├── Campaigns (brand activations)
│   └── CampaignInfluencers (invite → completed)
│       └── Publications (posts, reels, vídeos)
│           └── PublicationMetricSnapshots (likes, comments, etc.)
├── FileAssets (media kit, comprovantes)
├── AuditLog (LGPD: quem fez o quê, quando, onde)
└── ConsentRecords (LGPD: consentimento de dados)
```

Princípios:
- **Soft delete**: `is_deleted` + `deleted_at` (nunca sobrescrever dados)
- **Histórico**: Snapshots imutáveis (métricas sempre crescentes)
- **Source tracking**: `source_type` (PUBLIC_API, OAUTH, MANUAL, IMPORT, CALCULATED)
- **Audit**: IP + user agent + diffs de mudanças

Ver [`schema.prisma`](./prisma/schema.prisma) para definições completas.

## 🔄 GitHub Workflow

```bash
# Clonar
git clone <repo-url>

# Criar feature branch
git checkout -b feature/minha-feature

# Fazer alterações, testar
npm run lint:fix
npm run type-check
npm test

# Commitar
git add .
git commit -m "feat: descrição clara da mudança"

# Push e abrir PR
git push origin feature/minha-feature
```

### Git Hooks (Husky)

- **pre-commit**: Lint + Type check (futuro)
- **pre-push**: Testes (futuro)

## 📚 Documentação

- **`ARCHITECTURE.md`** — Visão geral da arquitetura (no projeto original: `/plano_arquitetura.md`)
- **`PROJECT_STRUCTURE.md`** — Detalhes da estrutura de pastas
- **`API.md`** — Especificação de APIs (a ser criado)
- **`SETUP.md`** — Instruções de deployment (VPS HostGator)

## 🐛 Troubleshooting

### "Error: P1000 — authentication failed for user"
- Verificar `DATABASE_URL` em `.env`
- Confirmar que PostgreSQL está rodando
- Verificar credenciais (usuário, senha, porta)

### "Error: NEXTAUTH_SECRET is not set"
- Gerar com: `openssl rand -base64 32`
- Adicionar a `.env`: `NEXTAUTH_SECRET=<valor-gerado>`

### "Error: PrismaClientRustPanicError"
- Limpar Prisma cache: `rm -rf node_modules/.prisma`
- Regenerar: `npm run db:generate`
- Reinstalar: `npm install`

## 📞 Suporte

Para dúvidas sobre:
- **Arquitetura**: Ver `/plano_arquitetura.md` (seções 1-14)
- **VPS setup**: Ver `/vps-setup/checklist.md` + scripts
- **Fases futuras**: Consultar roadmap em `/plano_arquitetura.md` seção 13

---

**Projeto criado em Phase 0 — Agosto 2026**
