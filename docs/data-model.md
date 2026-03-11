# Modelagem de Dados (Proposta Inicial - Gamificação)

Para sustentar a arquitetura descrita no PRD, separando os dados de autenticação dos dados da sub-aplicação de gamificação.

## Entidades Principais

### `User` (Autenticação Base - Já Existente no Boilerplate)
- `id` (UUID/Integer)
- `email` (String, Unique)
- `password` (Hash)
- `firstName`, `lastName` (String)
- `role` (Relacionamento Roles/Admin/User)

### `GamificationProfile` (Perfil e Carteira do Usuário)
- `id` (UUID)
- `userId` (FK -> User, Unique)
- `username` (String, Unique) - _@handle_ do usuário para facilitar a busca e menções.
- `totalXp` (Int) - Pontuação histórica global (nunca decai, "Hall da Fama").
- `currentMonthlyXp` (Int) - Pontos válidos para o ranking do mês atual.
- `currentYearlyXp` (Int) - Pontos válidos para o ranking do ano atual.
- `gratitudeTokens` (Int) - Saldo de tokens do mês para doar a outros membros (reseta todo mês).

### `Activity` (O Catálogo de Pontos)
- `id` (UUID)
- `title` (String) - Ex: _"Aplicar Mentoria"_, _"Corrigir PR no GitHub"_.
- `description` (String) - Descrição clara do que compõe a atividade.
- `fixedReward` (Int) - Quantidade padrão de pontos fornecidos.
- `isHidden` (Boolean) - Se `true`, não aparece na lista pública (pontuação oculta via URL secreta/QR Code).
- `requiresProof` (Boolean) - Se o sistema vai exigir preenchimento do campo `proofUrl`.
- `cooldownHours` (Int) - Define o intervalo de horas mínimo antes que o usuário possa refazer esta atividade (Evita bot/spam no catálogo).

### `Submission` (A Solicitação / Prova do Usuário)
- `id` (UUID)
- `userId` (FK -> User)
- `activityId` (FK -> Activity)
- `proofUrl` (String, Nullable) - Link (do GitHub, Linkedin) ou anexo no S3 (imagem/certificado).
- `status` (Enum: `PENDING`, `APPROVED`, `REJECTED`)
- `reviewerId` (FK -> User) - Quem foi o auditor que analisou o caso.
- `reviewedAt` (Date)
- `feedback` (String, Nullable) - Motivo da rejeição ou explicação do ajuste.
- `awardedXp` (Int, Nullable) - Os pontos efetivamente dados (o moderador pode decidir dar uma pontuação menor/maior dependendo da qualidade do que foi feito se a atividade for coringa).

### `Transaction` (Extrato e Auditoria)
Esta tabela é o **Log Imutável** do sistema. O saldo final do `GamificationProfile` sempre tem que bater com a soma das transactions dele.
- `id` (UUID)
- `profileId` (FK -> GamificationProfile)
- `amount` (Int) - Positivo (ganho) ou Negativo (penalidade/remoção).
- `type` (Enum: `SUBMISSION_APPROVED`, `GRATITUDE_RECEIVED`, `GRATITUDE_SENT`, `AUDIT_REWARD`, `PENALTY`)
- `referenceId` (UUID, Nullable) - PK da Tabela onde o evento se originou (Submission, Token Transfer, etc).
