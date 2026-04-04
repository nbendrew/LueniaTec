================================================================================
CONTEXTO DO CHAT — LLM / AGENTE DE IA
================================================================================
Última atualização: março/2025
Versão: 1.0

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
- Escolha e avaliação do modelo (OpenAI, Claude, Gemini)
- Arquitetura e escrita do system prompt
- Injeção dinâmica de cardápio e promoções
- Gerenciamento de contexto e histórico
- Tratamento de pedidos ambíguos ou incompletos
- Prevenção de alucinação (itens fora do cardápio)
- Comportamento do agente em situações de borda


================================================================================
STATUS ATUAL
================================================================================

LLM escolhida: em avaliação (OpenAI / Claude / Gemini)
System prompt: ainda não desenvolvido
Contexto dinâmico: planejado, não implementado


================================================================================
DECISÕES TÉCNICAS RELEVANTES
================================================================================

- O cardápio NÃO será hard-coded no system prompt
  → Será consultado dinamicamente no Supabase a cada chamada

- As promoções ativas serão injetadas no system prompt pelo N8N
  → Consultadas na tabela `promocoes` antes de cada chamada à LLM

- O histórico completo da conversa será enviado como contexto a cada chamada
  → Formato: array de objetos {role, content} ordenados por criado_em
  → Resolve o problema do agente "esquecer" o pedido entre mensagens


================================================================================
DESAFIOS TÉCNICOS MAPEADOS
================================================================================

- Pedidos ambíguos ou incompletos (ex: "uma pizza" sem especificar sabor)
- Múltiplos itens na mesma mensagem
- Mudança de pedido no meio da conversa
- Endereços incompletos
- Itens fora do cardápio (evitar que a IA invente itens inexistentes)
- Tratamento de áudios (transcrição antes de passar para a LLM)


================================================================================
PENDÊNCIAS / PRÓXIMOS PASSOS NESTE TEMA
================================================================================

- Definir qual LLM será usada no MVP
- Desenvolver o system prompt base do agente
- Planejar como o cardápio será formatado para o prompt
- Planejar como as promoções serão injetadas
- Testar comportamento em situações de borda


================================================================================
HISTÓRICO DE DECISÕES E APRENDIZADOS
================================================================================

(Este bloco será atualizado conforme o chat evoluir)

================================================================================
