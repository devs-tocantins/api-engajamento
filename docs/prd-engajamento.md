# Engajamento e Gamificação - Devs Tocantins

## 1. O Mantra do Sistema
Nossa filosofia é clara e inegociável: tudo que for feito em prol da comunidade e que gere um impacto real — por menor ou mais simples que seja — vale pontos e será reconhecido. Desde palestrar em um evento estadual até ajudar a corrigir um erro de digitação no código de um júnior no grupo do WhatsApp, toda contribuição importa. O objetivo deste sistema é capturar, registrar e eternizar esse esforço.

## 2. O Contexto e o Problema
A comunidade Devs Tocantins gera (ou irá gerar) um volume de valor diário: membros tiram dúvidas técnicas complexas, compartilham vagas de emprego, organizam eventos e desenvolvem projetos open-source.
O problema atual é a invisibilidade dessas contribuições. Como a maior parte das interações ocorre em grupos de mensagens efêmeros, o histórico de quem faz a comunidade acontecer se perde com o tempo. Não há uma forma centralizada de medir, reconhecer ou valorizar os membros mais ativos.

## 3. A Solução
A proposta é a construção de um **Motor de Gamificação e Reconhecimento**. O objetivo central é a criação de um ecossistema que gere prova social, histórico público e senso de pertencimento através de rankings, histórico de contribuições e badges (medalhas).

## 4. Visão Arquitetural e Estratégia do MVP
Para garantir que o projeto saia do papel com rapidez e qualidade, adotaremos uma abordagem focando no que é essencial para o MVP rodar e com a fundação preparada para escalar.

- **Abordagem API-First:** O desenvolvimento inicial será focado no Backend. Construiremos e testaremos a API antes de iniciar a construção do Frontend (React + Vite).
- **Autenticação Evolutiva:** Para evitar o overhead de infraestrutura no MVP, iniciaremos com um sistema de login próprio (E-mail/Senha com JWT). A migração para um Identity Provider robusto (Keycloak) ocorrerá em uma fase posterior, quando o ecossistema exigir.
- **Desacoplamento de Dados (Preparação para Ecossistema):** A modelagem do banco de dados já nascerá segmentada. A tabela principal de `Users` conterá apenas dados de autenticação (ID, Nome, Email). Todos os dados de pontuação ficarão isolados em uma tabela `Gamification_Profile`. Isso é crucial porque pavimenta o caminho para o nosso próximo serviço (o Mural de Vagas), permitindo que o usuário utilize a mesma conta base para acessar diferentes sistemas da comunidade.

## 5. Stack Tecnológica Base
- **Backend:** Node.js com NestJS (arquitetura modular, injeção de dependências e tipagem forte).
- **Banco de Dados:** PostgreSQL (c/ TypeORM).
- **Storage:** Integração com serviços compatíveis com S3 para armazenamento de comprovantes (prints/certificados).

## 6. Core Features e Regras de Negócio
O MVP implementará regras para mapear o engajamento, garantir a justiça do ranking e recompensar quem ajuda a manter a plataforma:

- **Catálogo de Atividades Fixas (O Core do Sistema):** Atividades pré-mapeadas com pontuações fixas. O usuário seleciona a atividade realizada e submete.
- **Atividades Ocultas (Eventos e Check-ins):** Criação de atividades que não aparecem no catálogo público. O usuário só consegue pontuar se acessar através de um link direto (ou QR Code). Ideal para registrar presença física em meetups e eventos fechados da comunidade.
- **Workflow de Moderação e Recompensa do Auditor:** Nada entra no ranking público sem validação (exceto regras automáticas). O usuário submete uma prova, e um Moderador aprova, ajusta ou rejeita. Para incentivar esse trabalho, o próprio Moderador ganha pontos automáticos no seu perfil a cada solicitação de usuário que ele audita.
- **Economia Peer-to-Peer (Tokens de Gratidão):** Mensalmente, cada membro recebe uma cota de "Tokens de Gratidão". Estes tokens devem ser transferidos para outros membros como agradecimento por ajudas no dia a dia. A transferência é automática, sem passar por moderação. Tokens não doados expiram no fim do mês.
- **Rankings Estratégicos:** O foco competitivo da comunidade estará nos Rankings Mensal e Anual. O Rank Global não sofrerá decaimento; ele existirá para sempre como um grande "Hall da Fama".
- **Gestão de Bounties e Atividades "Curinga":** Moderadores poderão cadastrar "Missões" com recompensas de alto valor para demandas específicas. Usuários poderão usar a atividade "Curinga" para sugerir pontos por contribuições não previstas.

---

## 7. Mecânica de Conquistas e Status (Adição Proposta)
- **Cálculo de Level:** Os usuários devem possuir um substatus gerado com base no XP histórico (_TotalXP_). Isso servirá de "Medidor de Confiança". Por exemplo, o usuário começa como `Newbie` e evolui passivamente até o Título Máximo (ex: `Lenda`).
- **Sistema de Badges / Medalhas:** Em contrapartida ao Ranking Dinâmico, o usuário destravará artes estáticas para enfeitar seu perfil público quando bater metas específicas (Ex: Badge "Ajudante da Madrugada" - respondeu a 10 perguntas de usuários transferindo Tokens de gratidão após 00h).

## 8. Segurança e Anti-Fraude (Adição Proposta)
Para que a Economia P2P e a escassez dos Rankings façam sentido, a plataforma necessita de:
- **Cooldowns e Rate Limiting:** Atividades simples do catálogo (Ex: Curtir o Post de alguém) devem ter uma limitação programática por horas/dias na tabela para estancar "farming".
- **Logs Imutáveis:** Cada ponto alterado na vida do usuário DEVE possuir uma justificativa (Transaction). O Moderador não aprova algo silenciosamente; deve haver uma Tabela `Transaction` documentando se o Mod X deu 50 pontos para o Usuário Y pelo motivo Z. 
- **Penalidades (Debit Points):** Suporte para administradores reverterem abusos debitando XP diretamente, caso um grupo se una para passar "Tokens de Gratidão" ilegais sem ter ocorrido ajuda de fato.
