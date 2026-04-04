================================================================================
CONTEXTO GERAL DO PROJETO — AGENTE DE IA PARA PEDIDOS VIA WHATSAPP
================================================================================
Última atualização: março/2025
Versão: 1.0

AVISO IMPORTANTE
----------------
Este arquivo é o contexto base do projeto. Cada chat temático possui seu próprio
arquivo de contexto específico na base de conhecimento, com atualizações regulares
conforme o projeto evolui. Sempre considere que os arquivos específicos de cada
chat podem conter informações mais recentes e detalhadas do que este arquivo geral.

Os arquivos de contexto específicos são:
- CONTEXTO_EVOLUTION_API.txt
- CONTEXTO_N8N.txt
- CONTEXTO_SUPABASE.txt
- CONTEXTO_LLM_AGENTE.txt
- CONTEXTO_INFRAESTRUTURA_VPS.txt
- CONTEXTO_NEGOCIO_PRODUTO.txt


================================================================================
SOBRE O PROJETO
================================================================================

Dois sócios desenvolvendo um sistema de atendimento automatizado via WhatsApp
com IA, focado em pizzarias e hamburguerias.

A IA atende o cliente do início ao fim — desde o "oi" até a finalização do
pedido, coletando escolha dos itens, endereço de entrega e método de pagamento.

Fase atual: desenvolvimento e testes. Principal dificuldade na parte técnica
do agente.


================================================================================
STACK UTILIZADA
================================================================================

- N8N              → orquestração dos fluxos
- Evolution API    → integração com WhatsApp (v2.3.7, hospedada na VPS)
- OpenAI / Claude / Gemini → LLM do agente (em avaliação)
- Supabase         → banco de dados (histórico, cardápio, promoções, pedidos)


================================================================================
ARQUITETURA DEFINIDA
================================================================================

Cliente (WhatsApp)
      ↓
Evolution API — recebe a mensagem
      ↓
N8N — orquestra o fluxo
      ↓
Supabase — busca histórico + cardápio + promoções ativas
      ↓
LLM (OpenAI/Claude/Gemini) — processa e gera resposta
      ↓
Supabase — salva mensagem do user e resposta da IA
      ↓
Evolution API — envia resposta ao cliente


================================================================================
DECISÕES TÉCNICAS JÁ TOMADAS
================================================================================

1. Cardápio não será hard-coded no system prompt — será consultado
   dinamicamente no Supabase a cada chamada.

2. Histórico de mensagens salvo no Supabase por número de telefone,
   enviado como contexto a cada chamada da LLM (resolve o problema
   do agente "esquecer" o pedido entre mensagens).

3. Como o N8N dispara um novo fluxo a cada mensagem recebida (e não
   mantém o fluxo aberto), a memória da conversa é totalmente
   gerenciada pelo Supabase.

4. Promoções dinâmicas gerenciadas via tabela no Supabase com campo
   `ativo`. O N8N consulta antes de cada chamada à LLM e injeta as
   promoções ativas no system prompt dinamicamente.
   - MVP: ativação via comando pelo WhatsApp
   - Futuro: painel web


================================================================================
INFRAESTRUTURA (VPS)
================================================================================

Provedor:   Hostinger, plano KVM 1
IP:         187.127.7.78
OS:         Ubuntu 24.04 LTS
Acesso:     ssh root@187.127.7.78

Containers Docker em /opt/evolution/:
- PostgreSQL 15       → porta 5432
- Redis Alpine        → porta 6379
- Evolution API v2.3.7 → porta 8080

URLs:
- Evolution API:  http://187.127.7.78:8080
- Dashboard:      http://187.127.7.78:8080/manager
- API Key:        tolokodedroga
- Instância ativa: wa4 (WhatsApp conectado)


================================================================================
ESTRUTURA DE PASTAS NA VPS
================================================================================

/opt/evolution/
├── postgres/
│   └── docker-compose.yaml
├── redis/
│   └── docker-compose.yaml
└── api/
    ├── docker-compose.yaml
    └── .env


================================================================================
DESAFIOS TÉCNICOS MAPEADOS
================================================================================

- Gerenciamento de sessão por número de telefone no N8N
- Tratamento de áudios enviados pelo cliente
- Pedidos ambíguos ou incompletos
- Múltiplos itens na mesma mensagem
- Mudança de pedido no meio da conversa
- Endereços incompletos
- Itens fora do cardápio (evitar alucinação da LLM)


================================================================================
ESTRUTURA DO BANCO DE DADOS (SUPABASE) — PLANEJADA
================================================================================

Tabela: contatos
- telefone   (text, chave primária)
- nome       (text)
- criado_em  (timestamp)

Tabela: mensagens
- id         (uuid, chave primária, gerado automaticamente)
- telefone   (text, chave estrangeira → contatos)
- role       (text: "user" ou "assistant")
- conteudo   (text)
- criado_em  (timestamp)

Tabelas futuras planejadas: cardapio, promocoes, pedidos


================================================================================
ROADMAP DE NEGÓCIO
================================================================================

Estágio atual: Estágio 1 — validação do produto e primeiros clientes
Trajetória planejada: Freelancer → Consultor → Agência → Professor


================================================================================
CHATS TEMÁTICOS DO PROJETO
================================================================================

O projeto está organizado em chats separados por tema. Cada chat tem seu
próprio arquivo de contexto que é atualizado conforme o desenvolvimento avança.
Ao iniciar um chat temático, considere sempre o contexto geral acima E o
contexto específico do tema em questão.

Temas e seus escopos:

1. EVOLUTION API
   Configuração, instâncias, webhooks, envio/recebimento de mensagens,
   tratamento de mídia (áudios, imagens).

2. N8N
   Instalação, fluxos, nós, lógica de orquestração, tratamento de erros,
   gerenciamento de sessão por telefone.

3. SUPABASE
   Estrutura do banco, tabelas, queries, integração com N8N,
   histórico de mensagens.

4. LLM / AGENTE DE IA
   System prompt, injeção de cardápio e promoções, gerenciamento de
   contexto/histórico, tratamento de pedidos ambíguos, prevenção de alucinação.

5. INFRAESTRUTURA / VPS
   Docker, containers, configurações da Hostinger, segurança, SSL futuro.

6. NEGÓCIO / PRODUTO
   Roadmap, precificação, primeiros clientes, painel web, funcionalidades futuras.

================================================================================s
