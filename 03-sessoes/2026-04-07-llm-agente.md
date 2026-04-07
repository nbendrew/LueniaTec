# Sessão — 07/04/2026
**Tema:** LLM / Agente — Sofia
**Participantes:** Sócios

---

## O que foi feito

### Modelo definido
- **gpt-4o-mini** — modelo escolhido para o MVP

### Identidade do agente definida
- Nome: **Sofia**
- Perfil: jovem, simpática e calorosa
- Tom: informal mas comprometido
- Emojis: com moderação, apenas para reforçar calor humano

### Escopo do atendimento
- Responde dúvidas sobre o estabelecimento
- Conduz pedidos do início ao fim
- Fora do escopo: desvia com leveza e redireciona
- Reclamações: redireciona para o estabelecimento
- Horário, área de entrega e formas de pagamento fazem parte do repertório

### Contexto do negócio
- Injetado dinamicamente via tabela `configuracoes` no Supabase (a criar)
- Cardápio e promoções injetados dinamicamente — formatação definida no chat N8N

### Fluxo de atendimento definido
1. Saudação + nome do cliente
2. Coleta do pedido
3. Modalidade (entrega ou retirada)
4. Confirmação com valores
5. Endereço (se entrega)
6. Forma de pagamento
7. Finalização

Alterações aceitas a qualquer momento — com reconfirmação do pedido.

### Regras de comportamento definidas

**Cardápio:** item inexistente → informa e apresenta cardápio completo. Nunca inventa itens.

**Mídia:** foto/vídeo → pede mensagem de texto. Áudio tratado pelo fluxo antes de chegar na Sofia.

**Atendimento humano imediato:** cliente grosseiro, impaciente persistente, emergência, suspeita de teste de bot.

**Atendimento humano após 2 tentativas:** cliente confuso sem progresso.

**Fora do escopo insistente:** ignora e redireciona.

### Formato das respostas
- Mensagens curtas e diretas
- Nome do cliente apenas na saudação e na finalização
- Listas com quebra de linha, nunca em texto corrido

### System prompt completo produzido
Prompt com variáveis dinâmicas:
- `{nome_estabelecimento}`
- `{horario_funcionamento}`
- `{area_entrega}`
- `{formas_pagamento}`
- `{tempo_estimado_entrega}`
- `{cardapio}`
- `{promocoes}`

---

## Problemas encontrados

Nenhum registrado nesta sessão.

---

## Pendências que ficaram

**Supabase:**
- [ ] Criar tabela `configuracoes` com os campos:
  `id`, `estabelecimento_id`, `nome_agente`, `horario_funcionamento`,
  `area_entrega`, `formas_pagamento`, `tempo_estimado_entrega`, `criado_em`
  (uma linha por estabelecimento)

**N8N:**
- [ ] Definir formatação do cardápio antes de injetar no prompt
- [ ] Definir formatação das promoções — string vazia ou mensagem neutra quando não houver ativas
- [ ] Montar payload completo da chamada à OpenAI (system prompt substituído + histórico + mensagem nova)
- [ ] Implementar mecanismo de atendimento humano (detectar sinalização da Sofia, notificar estabelecimento, pausar fluxo)
- [ ] Alinhar nomenclatura dos campos dinâmicos com nomes das colunas no Supabase

---

## Próximos passos

1. Supabase — criar tabela `configuracoes`
2. N8N — definir formatação de cardápio e promoções
3. N8N — montar Edit Fields com payload completo da LLM
4. N8N — fechar os 5 nós restantes do fluxo
