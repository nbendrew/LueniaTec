# Backlog
Última atualização: 07/04/2026

---

## LLM / Agente

- [ ] Definir qual LLM será usada no MVP (OpenAI / Claude / Gemini)
- [ ] Desenvolver system prompt base do agente
- [ ] Definir formato de injeção do cardápio no prompt
- [ ] Definir formato de injeção das promoções no prompt
- [ ] Testar comportamento em situações de borda:
  - Pedido ambíguo ou incompleto ("uma pizza" sem sabor)
  - Múltiplos itens na mesma mensagem
  - Mudança de pedido no meio da conversa
  - Endereço incompleto
  - Item fora do cardápio (prevenção de alucinação)

---

## N8N

- [ ] Tratamento de áudios — transcrição antes de passar para a LLM
- [ ] Tratamento de erros no fluxo (o que fazer se a LLM não responder, se o Supabase falhar, etc.)

---

## Supabase

- [ ] Definir estrutura definitiva da tabela `pedidos` para registro de pedidos finalizados
- [ ] Planejar como o agente vai registrar um pedido finalizado (trigger ou nó específico no N8N)

---

## Infraestrutura

- [ ] Configurar firewall — revisar portas expostas desnecessariamente
- [ ] Planejar SSL/HTTPS com domínio próprio
- [ ] Configurar monitoramento básico dos containers (uptime, restart automático)

---

## Negócio / Produto

- [ ] Formalizar abertura do ME no Simples Nacional (CNAE 6201-5/01)
- [ ] Contratar contador — escritório digital, R$100–150/mês
- [ ] Definir precificação do MVP
- [ ] Identificar e abordar primeiros clientes para teste
- [ ] Planejar escopo e interface do painel web (gerenciamento de cardápio, promoções e pedidos)
- [ ] Planejar ativação/desativação de promoções via comando no WhatsApp (MVP)

## Skills — Luenia Foods

- [ ] Construir skill de personal branding
- [ ] Construir skill de prova social e cases (aguarda clientes reais)

