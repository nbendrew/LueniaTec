================================================================================
CONTEXTO DO CHAT — SUPABASE
================================================================================
Última atualização: 09/04/2026
Versão: 1.2

AVISO IMPORTANTE
----------------
Este arquivo é atualizado regularmente conforme o projeto evolui.
Sempre considere que este arquivo pode conter informações mais recentes
do que o CONTEXTO_GERAL_PROJETO.txt.
Leia também o CONTEXTO_GERAL_PROJETO.txt para o contexto completo do projeto.


================================================================================
ESCOPO DESTE CHAT
================================================================================

Este chat cobre tudo relacionado ao Supabase:
- Criação e estrutura das tabelas
- Queries utilizadas pelo N8N
- Gerenciamento de histórico de mensagens
- Cardápio e promoções dinâmicas
- Registro de pedidos


================================================================================
STATUS ATUAL DO SUPABASE
================================================================================

Projeto:        agente-ia
Região:         São Paulo (sa-east-1)
URL:            https://wyngopoqjvegqpvttjxb.supabase.co
Status:         ACTIVE_HEALTHY
PostgreSQL:     versão 17
Plano:          Free (Cloud) — VPS sem RAM suficiente para self-hosted
Acesso MCP:     ativo — Claude acessa o banco diretamente sem SQL Editor

Todas as tabelas criadas, validadas e com RLS ativo.


================================================================================
ESTRUTURA DO BANCO DE DADOS — IMPLEMENTADA
================================================================================

Arquitetura: multi-tenant — `estabelecimento_id` presente em todas as tabelas.

--- Tabela: estabelecimentos ---
Objetivo: registro dos clientes (donos de restaurante)
Status: ✓ RLS ativo — 3 registros

--- Tabela: contatos ---
Objetivo: clientes que entram em contato via WhatsApp
Constraint: UNIQUE(estabelecimento_id, telefone)
  → mesmo cliente pode existir em múltiplos estabelecimentos sem conflito
Status: ✓ RLS ativo — 4 registros

--- Tabela: mensagens ---
Objetivo: histórico completo das conversas
Chave estrangeira: contato_id → contatos (declarada formalmente)
Campo role: "user" ou "assistant"
  → formato exigido pela LLM: [{role, content}] ordenado por criado_em ASC
Status: ✓ RLS ativo — 16 registros

--- Tabela: cardapio ---
Objetivo: itens disponíveis, consultados dinamicamente a cada chamada
Formato: uma linha por variação (ex: Calabresa M e Calabresa G como registros separados)
Obs: tamanhos como registros individuais — simplifica a lógica do MVP
Status: ✓ RLS ativo — dados de teste inseridos (Calabresa M/G, 4 Queijos M/G, Hamburguer Clássico, Coca-Cola Lata)

--- Tabela: promocoes ---
Objetivo: promoções com campo `ativo` (boolean)
Status: ✓ RLS ativo

--- Tabela: pedidos ---
Objetivo: registro dos pedidos finalizados
Campo itens: JSONB — flexível para o MVP
Status: ✓ RLS ativo — sem registros ainda

--- Tabela: configuracoes ---
Objetivo: dados de configuração do agente por estabelecimento
Campos: id, estabelecimento_id, nome_agente, horario_funcionamento, area_entrega,
        formas_pagamento, tempo_estimado_entrega, observacoes (nullable), criado_em
Obs: campo `observacoes` nullable — N8N injeta string vazia quando ausente
Status: ✓ RLS ativo — registro de teste vinculado ao Teste_Estabelecimento


================================================================================
DADOS DE TESTE INSERIDOS
================================================================================

Estabelecimento: `Teste_Estabelecimento` — ativo
Cardápio: Calabresa M, Calabresa G, 4 Queijos M, 4 Queijos G, Hamburguer Clássico, Coca-Cola Lata
Configuracoes: registro vinculado ao Teste_Estabelecimento com todos os campos preenchidos

Fluxo de onboarding para novo cliente:
Passar os dados e cardápio no chat → Claude cadastra tudo via MCP sem precisar do SQL Editor.


================================================================================
LÓGICA DE PROMOÇÕES DINÂMICAS
================================================================================

- Tabela `promocoes` terá campo `ativo` (boolean)
- O N8N consulta esta tabela antes de cada chamada à LLM
- Promoções ativas são injetadas dinamicamente no system prompt
- MVP: dono ativa/desativa via comando pelo WhatsApp
- Futuro: painel web para gerenciamento


================================================================================
PENDÊNCIAS / PRÓXIMOS PASSOS NESTE TEMA
================================================================================

- Reativar RLS com políticas corretas antes de ir para produção
  (atualmente ativo mas sem políticas definidas — acesso via service_role)
- Definir estrutura definitiva da tabela `pedidos` para registro de pedidos finalizados
- Planejar como o agente vai registrar um pedido finalizado (trigger ou nó no N8N)


================================================================================
HISTÓRICO DE DECISÕES E APRENDIZADOS
================================================================================

07/04/2026 — Arquitetura
  Multi-tenant com estabelecimento_id em todas as tabelas.
  Supabase Cloud plano Free — VPS sem RAM suficiente para self-hosted.

07/04/2026 — Cardápio
  Tamanhos e adicionais adiados para depois do MVP.

07/04/2026 — Pedidos
  Campo itens usando JSONB — flexível para o MVP.

07/04/2026 — Contatos
  UNIQUE(estabelecimento_id, telefone) — mesmo cliente em múltiplos
  estabelecimentos sem conflito de chave.

07/04/2026 — Mensagens
  Chave estrangeira contato_id declarada formalmente.
  RLS corrigido em estabelecimentos e contatos que estavam sem.

07/04/2026 — Acesso MCP
  Claude acessa o banco diretamente via MCP do Supabase.
  Não é necessário usar o SQL Editor para consultas e alterações.

09/04/2026 — Cardápio
  Formato definido: uma linha por variação (ex: Calabresa M e Calabresa G como registros separados).
  Simplifica a lógica do MVP sem precisar de campos de tamanho/adicionais.

09/04/2026 — Tabela configuracoes
  Campo `observacoes` adicionado como nullable (text).
  Quando vazio, N8N injeta string vazia — Sofia ignora o bloco naturalmente.

================================================================================
