# Phase 0 — Development Setup Checklist

**Objetivo**: Preparar a estrutura base do projeto (schema, auth, CRUD influencers/campaigns, layouts).

**Timeline estimado**: 2-3 semanas (solo)

---

## 📋 Checklist de Tarefas

### Phase 0.0 — Project Initialization ✅
- [x] Definir arquitetura completa (schema.prisma, estrutura de pastas)
- [x] Criar package.json com dependências
- [x] Criar configurações (tsconfig, tailwind, eslint, prettier)
- [x] Criar .gitignore
- [ ] **Seu turno**: Criar repositório Git local/remoto
  ```bash
  cd influenciadores-app
  git init
  git add .
  git commit -m "chore: initial project setup"
  git remote add origin <seu-repo-url>
  git push -u origin main
  ```

---

### Phase 0.1 — Folder Structure & Base Pages

- [ ] Criar estrutura de pastas conforme `PROJECT_STRUCTURE.md`
  ```bash
  mkdir -p src/{app,lib,types,actions,components,hooks}
  mkdir -p src/app/{auth,api}
  mkdir -p src/components/{auth,layout,common}
  ```

- [ ] Criar arquivo `src/app/layout.tsx` (Root layout)
  - [ ] Provider setup (Auth.js, outros)
  - [ ] Meta tags (title, description, etc.)
  - [ ] Global CSS imports
  - [ ] Tailwind classes

- [ ] Criar páginas básicas
  - [ ] `src/app/(auth)/login/page.tsx`
  - [ ] `src/app/(auth)/signup/page.tsx`
  - [ ] `src/app/(auth)/forgot-password/page.tsx`
  - [ ] `src/app/(app)/layout.tsx` (App layout com sidebar)
  - [ ] `src/app/(app)/dashboard/page.tsx`
  - [ ] `src/app/not-found.tsx`
  - [ ] `src/app/error.tsx`

- [ ] Criar componentes de layout
  - [ ] `src/components/layout/Sidebar.tsx`
  - [ ] `src/components/layout/Topbar.tsx`
  - [ ] `src/components/layout/NavLink.tsx`

---

### Phase 0.2 — Authentication (Auth.js v5)

- [ ] Configurar Auth.js em `src/lib/auth.ts`
  - [ ] NextAuth config (providers, callbacks, session strategy)
  - [ ] Prisma adapter
  - [ ] JWT encoding/decoding se usar JWT

- [ ] Criar API route: `src/app/api/auth/[...nextauth]/route.ts`
  - [ ] Integrar com auth.ts
  - [ ] Testar login/logout

- [ ] Criar auth actions em `src/actions/auth-actions.ts`
  - [ ] `signUp()` — registrar novo usuário
  - [ ] `signIn()` — login (servidor)
  - [ ] `signOut()` — logout
  - [ ] Hash de senha com bcryptjs

- [ ] Criar componentes de auth
  - [ ] `src/components/auth/LoginForm.tsx` (form + action)
  - [ ] `src/components/auth/SignupForm.tsx` (form + action)
  - [ ] `src/components/auth/LogoutButton.tsx`

- [ ] Criar middleware: `src/middleware.ts`
  - [ ] Proteger rotas privadas (redirecion para /login se não autenticado)
  - [ ] Permitir acesso público a /login, /signup, /forgot-password

- [ ] Testar fluxo completo
  - [ ] Signup → criar usuário no DB
  - [ ] Login → sessão ativa
  - [ ] Logout → sessão removida
  - [ ] Redirect para login se não autenticado

---

### Phase 0.3 — Database & Prisma

- [ ] Criar arquivo `.env` com DATABASE_URL (PostgreSQL local)
  ```env
  DATABASE_URL=postgresql://postgres:password@localhost:5432/influenciadores_dev
  NEXTAUTH_SECRET=<gere com openssl rand -base64 32>
  NEXTAUTH_URL=http://localhost:3000
  ```

- [ ] Adicionar schema.prisma ao projeto (copiar de `schema.prisma`)
  ```bash
  cp schema.prisma prisma/
  ```

- [ ] Gerar Prisma client
  ```bash
  npm run db:generate
  ```

- [ ] Criar primeira migration
  ```bash
  npm run db:migrate:dev --name init
  ```

- [ ] Verificar schema no Prisma Studio
  ```bash
  npm run db:studio
  ```

---

### Phase 0.4 — RBAC & Permissions

- [ ] Criar `src/lib/permissions.ts`
  - [ ] `checkPermission(userId, resourceType, action)` — verificar se usuário tem permissão
  - [ ] `getUserRole(userId)` — buscar role do usuário
  - [ ] Funções helper: `canViewInfluencer()`, `canEditCampaign()`, etc.

- [ ] Criar middleware de permissões em `src/middleware.ts`
  - [ ] Verificar autenticação + permissões nas rotas privadas

- [ ] Inicializar permissões na criação do usuário
  - [ ] ADMIN: todas as permissões
  - [ ] MANAGER: view/create/edit (não delete)
  - [ ] ANALYST: view/export (read-only)
  - [ ] VIEWER: view (read-only)

---

### Phase 0.5 — Validators (Zod)

- [ ] Criar `src/lib/validators.ts` com schemas Zod para:
  - [ ] `signUpSchema` — nome, email, senha
  - [ ] `createInfluencerSchema` — nome, email, categoria, bio
  - [ ] `createCampaignSchema` — nome, brand, budget, datas
  - [ ] `updateInfluencerSchema`
  - [ ] `updateCampaignSchema`
  - [ ] Outros conforme necessário

- [ ] Usar validators em:
  - [ ] Auth actions
  - [ ] API routes
  - [ ] Formulários (error handling)

---

### Phase 0.6 — Influencer CRUD (Basics)

**Rotas**:
- GET `/influencers` — listar (com filtros)
- POST `/influencers` — criar
- GET `/influencers/[id]` — detalhe
- PATCH `/influencers/[id]` — atualizar
- DELETE `/influencers/[id]` — soft delete

- [ ] Criar actions: `src/actions/influencer-actions.ts`
  - [ ] `getInfluencers(organizationId, filters?)` — buscar com paginação
  - [ ] `createInfluencer(data)` — criar + audit log
  - [ ] `updateInfluencer(id, data)` — atualizar + audit log
  - [ ] `deleteInfluencer(id)` — soft delete + audit log

- [ ] Criar API routes: `src/app/api/influencers/route.ts` + `[id]/route.ts`
  - [ ] GET, POST, PATCH, DELETE
  - [ ] Validação com Zod
  - [ ] Verificação de permissões

- [ ] Criar páginas UI
  - [ ] `src/app/(app)/influencers/page.tsx` — lista (tabela com filtros)
  - [ ] `src/app/(app)/influencers/new/page.tsx` — criar novo
  - [ ] `src/app/(app)/influencers/[id]/page.tsx` — detalhe + editar

- [ ] Criar componentes reutilizáveis
  - [ ] `src/components/influencers/InfluencerForm.tsx` — create/edit form
  - [ ] `src/components/influencers/InfluencerTable.tsx` — lista em tabela
  - [ ] `src/components/influencers/InfluencerCard.tsx` — preview card
  - [ ] `src/components/common/Pagination.tsx` — paginação
  - [ ] `src/components/common/SearchBox.tsx` — filtro por nome/email

- [ ] Criar hook customizado
  - [ ] `src/hooks/useInfluencers.ts` — fetch/mutate influencers

- [ ] Testar CRUD completo
  - [ ] Criar influenciador
  - [ ] Listar com filtros
  - [ ] Editar
  - [ ] Deletar (verificar soft delete)
  - [ ] Verificar audit logs

---

### Phase 0.7 — Campaign CRUD (Basics)

**Rotas**:
- GET `/campaigns` — listar
- POST `/campaigns` — criar
- GET `/campaigns/[id]` — detalhe
- PATCH `/campaigns/[id]` — atualizar
- DELETE `/campaigns/[id]` — soft delete

- [ ] Criar actions: `src/actions/campaign-actions.ts`
  - [ ] `getCampaigns(organizationId)`
  - [ ] `createCampaign(data)` — criar + audit log
  - [ ] `updateCampaign(id, data)` — atualizar
  - [ ] `deleteCampaign(id)` — soft delete

- [ ] Criar API routes: `src/app/api/campaigns/route.ts` + `[id]/route.ts`

- [ ] Criar páginas UI
  - [ ] `src/app/(app)/campaigns/page.tsx` — lista
  - [ ] `src/app/(app)/campaigns/new/page.tsx` — criar
  - [ ] `src/app/(app)/campaigns/[id]/page.tsx` — detalhe

- [ ] Criar componentes
  - [ ] `src/components/campaigns/CampaignForm.tsx`
  - [ ] `src/components/campaigns/CampaignCard.tsx`
  - [ ] `src/components/campaigns/CampaignSelector.tsx` (dropdown para usar ao atribuir influenciadores)

- [ ] Testar CRUD completo

---

### Phase 0.8 — Audit Logging

- [ ] Criar `src/lib/audit.ts`
  - [ ] `logAudit(userId, entityType, entityId, action, changes, ipAddress)` — registrar ação
  - [ ] Extrair IP do request (`x-forwarded-for` ou `socket.remoteAddress`)

- [ ] Integrar em todas as actions (influencer, campaign, auth)
  - [ ] CREATE → log "created"
  - [ ] UPDATE → log "updated" com diffs
  - [ ] DELETE → log "deleted"

- [ ] Criar página de auditoria (básica)
  - [ ] `src/app/(app)/settings/audit-log/page.tsx` — listar logs (admin only)
  - [ ] Filtros por data, usuário, entidade
  - [ ] Ver diffs de mudanças

---

### Phase 0.9 — Testing & Polish

- [ ] Testar autenticação
  - [ ] Login/signup/logout funcionam
  - [ ] JWT/session válido
  - [ ] Logout limpa session

- [ ] Testar RBAC
  - [ ] ADMIN consegue criar/editar/deletar
  - [ ] MANAGER consegue criar/editar (não delete)
  - [ ] ANALYST consegue apenas visualizar
  - [ ] Rotas retornam 403 se não tem permissão

- [ ] Testar validações
  - [ ] Emails inválidos rejeitados
  - [ ] Campos obrigatórios validados
  - [ ] Mensagens de erro claras

- [ ] Verificar UX
  - [ ] Spinner durante carregamento
  - [ ] Mensagens de sucesso/erro
  - [ ] Confirmação antes de deletar
  - [ ] Responsive design (mobile, tablet, desktop)

- [ ] Code quality
  ```bash
  npm run lint:fix
  npm run format
  npm run type-check
  ```

- [ ] Documentar decisões em comentários
  - [ ] Por que Zod e não yup?
  - [ ] Por que soft delete e não hard delete?
  - [ ] Etc.

---

### Phase 0.10 — Deployment Prep (Opcional para agora)

- [ ] Criar arquivo `docker-compose.yml` para rodar PostgreSQL + Redis local (futuro)
- [ ] Criar instruções de deploy para HostGator VPS
  - [ ] Copiar para o servidor
  - [ ] Rodar `npm install`, `npm run db:migrate:deploy`
  - [ ] Usar PM2 para manter app rodando
  - [ ] Configurar Nginx como proxy reverso

Veja `/vps-setup/checklist.md` para detalhes.

---

## 📊 Progresso

```
Phase 0.0 — Initialization ✅
Phase 0.1 — Folder Structure ⏳
Phase 0.2 — Auth ⏳
Phase 0.3 — Database ⏳
Phase 0.4 — RBAC ⏳
Phase 0.5 — Validators ⏳
Phase 0.6 — Influencer CRUD ⏳
Phase 0.7 — Campaign CRUD ⏳
Phase 0.8 — Audit Logging ⏳
Phase 0.9 — Testing & Polish ⏳
Phase 0.10 — Deploy Prep ⏳
```

---

## 🔗 Referências

- **Architecture**: `/plano_arquitetura.md`
- **Project Structure**: `PROJECT_STRUCTURE.md`
- **Database Schema**: `prisma/schema.prisma`
- **VPS Setup**: `/vps-setup/checklist.md`

---

## ⚠️ Notas Importantes

1. **Soft Delete**: Sempre usar `is_deleted` + `deleted_at`, nunca deletar records permanentemente em dev/prod.
2. **Audit Logging**: Logar TUDO (create, update, delete) com IP + user agent para LGPD compliance.
3. **Permission Checks**: Verificar permissões em TODAS as actions/API routes.
4. **Validators**: Validar ALL inputs com Zod, never trust client.
5. **Type Safety**: Sempre rodar `npm run type-check` antes de commitar.
6. **Multi-tenant**: SEMPRE filtrar por `organization_id` nas queries.

---

**Status**: Fase 0 em preparação — Agosto 2026
