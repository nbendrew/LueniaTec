# Sessão — 09/04/2026
**Tema:** LLM / Agente — Revisão e alinhamento do system prompt
**Participantes:** Sócios

---

## O que foi feito

### System prompt revisado e confirmado
- Versão atual do system prompt da Sofia validada
- Sem alterações de conteúdo em relação à versão anterior
- Prompt está operacional e pronto para uso no fluxo N8N

### Alinhamentos registrados

**Supabase:**
- Coluna `observacoes` do tipo `text`, nullable, adicionada à tabela `configuracoes`
- Quando vazio: N8N injeta string vazia no campo `{observacoes}`

**N8N:**
- Quando `observacoes` estiver vazio, injetar string vazia
- Sofia ignora o bloco naturalmente — sem necessidade de lógica adicional no prompt

---

## Pendências que ficaram

- [ ] Testar comportamento em situações de borda (pedido ambíguo, item fora do cardápio, etc.)
- [ ] Implementar mecanismo de atendimento humano no N8N

---

## Próximos passos

1. Teste de ponta a ponta com WhatsApp real
2. Validar comportamento da Sofia com cardápio real do Teste_Estabelecimento
