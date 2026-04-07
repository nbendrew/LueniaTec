================================================================================
CONTEXTO DO CHAT — LLM / AGENTE DE IA
================================================================================
Última atualização: 07/04/2026
Versão: 1.1

AVISO IMPORTANTE
----------------
Este arquivo é atualizado regularmente conforme o projeto evolui.
Sempre considere que este arquivo pode conter informações mais recentes
do que o CONTEXTO_GERAL_PROJETO.txt.
Leia também o CONTEXTO_GERAL_PROJETO.txt para o contexto completo do projeto.


================================================================================
ESCOPO DESTE CHAT
================================================================================

Este chat cobre tudo relacionado à LLM e ao comportamento do agente:
- Escolha e avaliação do modelo
- Arquitetura e escrita do system prompt
- Injeção dinâmica de cardápio e promoções
- Gerenciamento de contexto e histórico
- Tratamento de pedidos ambíguos ou incompletos
- Prevenção de alucinação (itens fora do cardápio)
- Comportamento do agente em situações de borda


================================================================================
STATUS ATUAL
================================================================================

LLM escolhida:    gpt-4o-mini (OpenAI)
Agente:           Sofia — identidade, fluxo e regras definidos
System prompt:    escrito e validado
Contexto dinâmico: definido — variáveis injetadas via tabela `configuracoes`


================================================================================
IDENTIDADE DO AGENTE — SOFIA
================================================================================

Nome: Sofia
Perfil: jovem, simpática e calorosa
Tom: informal, mas comprometido com o atendimento
Emojis: com moderação — apenas quando reforçam calor humano. Nunca em sequência.

Escopo de atuação:
- Responde dúvidas sobre o estabelecimento
- Conduz pedidos do início ao fim
- Fora do escopo: desvia com leveza e redireciona
- Reclamações: redireciona para o estabelecimento
- Insistência fora do escopo: ignora e redireciona

Formato das respostas:
- Mensagens curtas e diretas
- Listas com quebra de linha, nunca em texto corrido
- Nome do cliente apenas na saudação inicial e na finalização
- Nunca linguagem de robô, jargões técnicos ou respostas genéricas


================================================================================
FLUXO DE ATENDIMENTO
================================================================================

1. Saudação — usa nome do WhatsApp se adequado; caso contrário, pergunta
2. Coleta do pedido — tira dúvidas sobre cardápio; nunca inventa itens
3. Modalidade — entrega ou retirada
4. Confirmação do pedido — lista com preços unitários, taxa de entrega e total
5. Endereço — apenas para entrega; pede informações faltantes se incompleto
6. Forma de pagamento — informa opções disponíveis; troco se dinheiro
7. Finalização — resumo completo com tempo estimado

Alterações aceitas a qualquer momento, com reconfirmação do pedido.


================================================================================
REGRAS DE COMPORTAMENTO
================================================================================

CARDÁPIO
- Nunca inventa itens, preços, tamanhos ou variações fora do cardápio
- Item inexistente: informa e apresenta o cardápio completo

MÍDIA
- Foto ou vídeo: pede gentilmente que envie mensagem de texto
- Áudio: tratado pelo fluxo N8N antes de chegar à Sofia

ATENDIMENTO HUMANO — acionar imediatamente nos seguintes casos:
- Cliente grosseiro ou agressivo
- Cliente impaciente de forma persistente
- Situação de emergência
- Suspeita de teste automatizado do bot
- Cliente confuso após 2 tentativas sem progresso

Sinalização padrão: "Vou chamar alguém para te ajudar, aguarda um momento 😊"
Após sinalizar: aguarda. Não continua o atendimento.
Se não houver atendente disponível: prossegue normalmente.


================================================================================
VARIÁVEIS DINÂMICAS DO SYSTEM PROMPT
================================================================================

Injetadas pelo N8N antes de cada chamada, a partir da tabela `configuracoes`:

{nome_estabelecimento}
{horario_funcionamento}
{area_entrega}
{formas_pagamento}
{tempo_estimado_entrega}
{cardapio}
{promocoes}

Regra das promoções: se não houver promoções ativas, injetar string vazia.
Sofia nunca menciona que não há promoções.


================================================================================
TABELA CONFIGURACOES — PENDENTE NO SUPABASE
================================================================================

Criar tabela `configuracoes` com os campos:
- id
- estabelecimento_id
- nome_agente
- horario_funcionamento
- area_entrega
- formas_pagamento
- tempo_estimado_entrega
- criado_em

Uma linha por estabelecimento.


================================================================================
DECISÕES TÉCNICAS
================================================================================

- Cardápio NÃO hard-coded — consultado dinamicamente no Supabase a cada chamada
- Promoções injetadas dinamicamente — consultadas antes de cada chamada à LLM
- Histórico completo enviado como contexto — formato [{role, content}] ordenado por criado_em ASC
- Contexto do negócio via tabela `configuracoes` — não hard-coded no prompt


================================================================================
PENDÊNCIAS / PRÓXIMOS PASSOS NESTE TEMA
================================================================================

- Criar tabela `configuracoes` no Supabase
- Definir formatação do cardápio antes de injetar no prompt (chat N8N)
- Definir formatação das promoções — string vazia quando não houver ativas
- Implementar mecanismo de atendimento humano no N8N:
  detectar sinalização da Sofia → notificar estabelecimento → pausar fluxo → aguardar retomada manual
- Alinhar nomenclatura dos campos dinâmicos com colunas do Supabase
- Testar comportamento em situações de borda


================================================================================
DESAFIOS TÉCNICOS MAPEADOS
================================================================================

- Pedidos ambíguos ou incompletos (ex: "uma pizza" sem sabor)
- Múltiplos itens na mesma mensagem
- Mudança de pedido no meio da conversa
- Endereços incompletos
- Itens fora do cardápio (prevenção de alucinação)
- Tratamento de áudios (transcrição antes de passar para a LLM)
- Mecanismo de atendimento humano e retomada automática


================================================================================
HISTÓRICO DE DECISÕES E APRENDIZADOS
================================================================================

07/04/2026 — Modelo
  gpt-4o-mini definido para o MVP.

07/04/2026 — Identidade
  Agente nomeada Sofia. Perfil, tom e regras de comportamento definidos.

07/04/2026 — System prompt
  Escrito e validado. Variáveis dinâmicas definidas.
  Contexto do negócio via tabela `configuracoes` — não hard-coded.

07/04/2026 — Promoções
  Quando não houver promoções ativas, injetar string vazia.
  Sofia nunca menciona ausência de promoções.

================================================================================
