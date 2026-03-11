# Design de API (Proposta Inicial MVP)

A modelagem RESTful dos endpoints principais que a aplicação do frontend vai consumir.
*(As rotas autênticas irão exigir JWT nos headers utilizando os controllers padrão do NestJS já embutidos na arquitetura).*

## 1. Perfil e Painel
Rotas focadas no visual do "Meu Perfil" e do Perfil Público.
- `GET /api/v1/gamification-profiles/me` - Retorna os dados do próprio usuário logado (nível, progresso, saldo de tokens).
- `GET /api/v1/gamification-profiles/:username` - Retorna o perfil público (Status do rank, badges, etc).

## 2. Atividades (Catálogo Core)
Rotas listando onde o Dev pode engajar.
- `GET /api/v1/activities` - Retorna apenas atividades catalogadas não-ocultas.
- `POST /api/v1/activities` **[ROLES: ADMIN/MODERATOR]** - Cadastro de nova regra/atividade.

## 3. Submissão (Gamificação Ativa)
O ciclo completo do usuário validando uma tarefa.
- `POST /api/v1/submissions` - Body: `{ activityId, proofUrl }`. Cria status PENDING.
- `GET /api/v1/submissions/me` - Listagem do status e histórico de submissões minhas (aba "Minhas Validações").

## 4. Fila da Auditoria (Gamificação Administrativa)
Para os moderadores gerenciarem e ganharem XP automático por validação.
- `GET /api/v1/submissions/pending` **[ROLES: MODERATOR]** - Fila de auditoria com paginação.
- `POST /api/v1/submissions/:id/review` **[ROLES: MODERATOR]** - Body: `{ status: 'APPROVED'|'REJECTED', awardedXp: number, feedback?: string }`.

## 5. Extrato e Economia
Rotas para o extrato de pontos e envio de tokens P2P.
- `GET /api/v1/transactions/me` - Retorna o log de como o usuário ganhou seus pontos.
- `POST /api/v1/tokens/transfer` - Body: `{ toProfileId, amount, feedbackMessage }`. Envia os "Tokens de Gratidão" peer-to-peer.

## 6. Rankings Completos
Leituras otimizadas para montar o painel competitivo e hall da fama.
- `GET /api/v1/rankings/monthly` (Filtra pelo currentMonthlyXp)
- `GET /api/v1/rankings/yearly` (Filtra pelo currentYearlyXp)
- `GET /api/v1/rankings/global` (Top Histórico usando totalXp)
