# Estrutura do Projeto — Influenciadores CRM

## Organização de pastas (Phase 0)

```
influenciadores-app/
├── .env                          # Variáveis de ambiente (NÃO fazer commit)
├── .env.example                  # Template (fazer commit)
├── .gitignore
├── package.json
├── package-lock.json
├── tsconfig.json                 # TypeScript config
├── next.config.js                # Next.js config
├── postcss.config.js             # Tailwind CSS config
├── tailwind.config.js            # Tailwind CSS theme
├── .prettierrc                    # Code formatting
├── .eslintrc.json                # Linting rules
│
├── prisma/
│   ├── schema.prisma             # ← Banco de dados
│   └── migrations/               # Histórico de schema (gerado por Prisma)
│
├── src/
│   ├── app/                      # Next.js 15 App Router
│   │   ├── layout.tsx            # Root layout (HTML, providers)
│   │   ├── page.tsx              # / — Dashboard (after login)
│   │   ├── not-found.tsx
│   │   ├── error.tsx
│   │   │
│   │   ├── (auth)/               # Grupo de rotas: sem layout padrão
│   │   │   ├── layout.tsx        # Auth layout (sem sidebar)
│   │   │   ├── login/page.tsx
│   │   │   ├── signup/page.tsx
│   │   │   └── forgot-password/page.tsx
│   │   │
│   │   ├── (app)/                # Grupo de rotas: com layout + sidebar
│   │   │   ├── layout.tsx        # App layout (sidebar, topbar)
│   │   │   ├── dashboard/page.tsx
│   │   │   │
│   │   │   ├── influencers/
│   │   │   │   ├── page.tsx      # Lista (com filtros)
│   │   │   │   ├── new/page.tsx  # Criar novo
│   │   │   │   └── [id]/
│   │   │   │       ├── page.tsx  # Detalhe + editar
│   │   │   │       ├── edit/page.tsx
│   │   │   │       └── social-accounts/page.tsx
│   │   │   │
│   │   │   ├── campaigns/
│   │   │   │   ├── page.tsx
│   │   │   │   ├── new/page.tsx
│   │   │   │   └── [id]/
│   │   │   │       ├── page.tsx
│   │   │   │       └── edit/page.tsx
│   │   │   │
│   │   │   ├── publications/
│   │   │   │   └── page.tsx      # Lista + view por campanha
│   │   │   │
│   │   │   ├── reports/
│   │   │   │   ├── page.tsx      # Relatórios (custom)
│   │   │   │   └── [slug]/page.tsx
│   │   │   │
│   │   │   └── settings/
│   │   │       ├── page.tsx      # Org settings
│   │   │       ├── users/page.tsx
│   │   │       └── integrations/page.tsx
│   │   │
│   │   └── api/                  # API routes (Server Functions + API endpoints)
│   │       ├── auth/
│   │       │   ├── [...nextauth]/route.ts  # NextAuth.js
│   │       │   └── session/route.ts
│   │       │
│   │       ├── influencers/
│   │       │   ├── route.ts      # POST (create), GET (list)
│   │       │   └── [id]/route.ts # GET, PATCH, DELETE
│   │       │
│   │       ├── campaigns/
│   │       │   └── route.ts
│   │       │
│   │       ├── publications/
│   │       │   └── route.ts
│   │       │
│   │       └── webhooks/         # Webhooks (para integração de dados futura)
│   │           └── social-metrics/route.ts
│   │
│   ├── lib/                      # Utilities, helpers, configs
│   │   ├── auth.ts               # Auth.js config
│   │   ├── db.ts                 # Prisma client singleton
│   │   ├── encryption.ts         # AES-256-GCM para OAuth tokens
│   │   ├── validators.ts         # Zod schemas para inputs
│   │   ├── permissions.ts        # Funções de verificação de permissão
│   │   └── utils.ts              # Helpers genéricos
│   │
│   ├── types/                    # TypeScript types & interfaces
│   │   ├── auth.ts
│   │   ├── influencer.ts
│   │   ├── campaign.ts
│   │   ├── publication.ts
│   │   └── api.ts
│   │
│   ├── actions/                  # Server Actions (mutações de dados)
│   │   ├── influencer-actions.ts
│   │   ├── campaign-actions.ts
│   │   ├── publication-actions.ts
│   │   ├── auth-actions.ts
│   │   └── file-actions.ts
│   │
│   ├── components/               # React Components (reutilizáveis)
│   │   ├── auth/
│   │   │   ├── LoginForm.tsx
│   │   │   ├── SignupForm.tsx
│   │   │   └── LogoutButton.tsx
│   │   │
│   │   ├── layout/
│   │   │   ├── Sidebar.tsx
│   │   │   ├── Topbar.tsx
│   │   │   └── NavLink.tsx
│   │   │
│   │   ├── influencers/
│   │   │   ├── InfluencerCard.tsx
│   │   │   ├── InfluencerForm.tsx
│   │   │   ├── InfluencerTable.tsx
│   │   │   ├── SocialAccountsList.tsx
│   │   │   └── MetricsChart.tsx
│   │   │
│   │   ├── campaigns/
│   │   │   ├── CampaignForm.tsx
│   │   │   ├── CampaignCard.tsx
│   │   │   └── CampaignSelector.tsx
│   │   │
│   │   ├── publications/
│   │   │   ├── PublicationCard.tsx
│   │   │   └── PublicationMetrics.tsx
│   │   │
│   │   ├── common/               # Componentes genéricos
│   │   │   ├── Button.tsx        # Wrapper sobre shadcn Button
│   │   │   ├── Modal.tsx
│   │   │   ├── Input.tsx
│   │   │   ├── Select.tsx
│   │   │   ├── Pagination.tsx
│   │   │   ├── SearchBox.tsx
│   │   │   ├── Badge.tsx
│   │   │   ├── Card.tsx
│   │   │   ├── Table.tsx
│   │   │   └── Spinner.tsx
│   │   │
│   │   └── icons/               # SVG icons (ou Lucide React)
│   │       └── *.tsx
│   │
│   ├── hooks/                    # Custom React hooks
│   │   ├── useAuth.ts
│   │   ├── useInfluencers.ts
│   │   ├── useCampaigns.ts
│   │   └── usePagination.ts
│   │
│   └── middleware.ts             # Middleware do Next.js (auth, redirects)
│
├── public/                       # Static files
│   ├── logo.svg
│   ├── favicon.ico
│   └── images/
│
└── docs/                         # Documentação do projeto
    ├── API.md                    # Especificação de APIs
    ├── ARCHITECTURE.md           # Referência rápida da arquitetura
    └── SETUP.md                  # Como rodar o projeto localmente
```

## Padrões de Arquitetura

### 1. **Server Actions vs API Routes**
- **Server Actions** (em `src/actions/`): Para mutações de dados (create, update, delete)
  - Roda no servidor, seguro, sem exposição de lógica
  - Usadas diretamente em formulários
- **API Routes** (em `src/app/api/`): Para integração com terceiros, webhooks, ou queries complexas
  - Mais explícitas que Server Actions
  - Melhor para clientes externos

### 2. **Validação com Zod**
- Todos os inputs (forms, API) validados com Zod schemas em `src/lib/validators.ts`
- Garante type-safety de ponta a ponta
- Exemplo:
  ```typescript
  export const createInfluencerSchema = z.object({
    name: z.string().min(1, "Nome é obrigatório"),
    email: z.string().email().optional(),
    ...
  });
  ```

### 3. **Permissões (RBAC)**
- Middleware em `src/middleware.ts` verifica autenticação
- Server Actions e API routes usam `checkPermission(userId, resourceType, action)` da `src/lib/permissions.ts`
- Exemplo:
  ```typescript
  await checkPermission(userId, "INFLUENCER", "EDIT");
  ```

### 4. **Audit Logging**
- Toda mutação (create, update, delete) logada via Server Action
- Function helper em `src/lib/audit.ts`:
  ```typescript
  await logAudit(userId, "Influencer", influencerId, "updated", changes, ipAddress);
  ```

### 5. **Encrypt/Decrypt OAuth Tokens**
- Em `src/lib/encryption.ts`: AES-256-GCM com `OAUTH_TOKEN_ENCRYPTION_KEY`
- Tokens são encrypted ANTES de salvar no DB
- Descriptografados apenas quando necessário usar a API

### 6. **Componentes com shadcn/ui**
- Todos os componentes de UI importados do `shadcn/ui`
- Customizados com Tailwind em `src/components/common/`
- Evita re-implementar buttons, inputs, modals, etc.

## Próximos Passos

1. ✅ schema.prisma definido
2. ⏳ **Phase 0.1**: Criar `src/` folder structure + basic pages
3. ⏳ **Phase 0.2**: Auth.js config + login/signup flows
4. ⏳ **Phase 0.3**: Dashboard template + sidebar layout
5. ⏳ **Phase 1**: Influencer CRUD (create, read, update, delete, soft-delete)
6. ⏳ **Phase 2**: Campaign CRUD + influencer assignment
7. ⏳ **Phase 3**: File storage (S3/R2) + media kit upload
8. ⏳ **Phase 4**: Publication tracking (manual + API scraping)
9. ⏳ **Phase 5**: OAuth flows (Instagram, TikTok, Facebook, YouTube)
10. ⏳ **Phase 6**: Reports, exports, LGPD compliance

---

**Nota**: Esta estrutura segue as best practices do Next.js 15 App Router e é escalável para futuras features.
