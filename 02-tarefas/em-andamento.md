# Em Andamento
Última atualização: 07/04/2026

---

## N8N — Fechamento do Fluxo Principal

### Próximos nós a construir (em ordem)

- [ ] Edit Fields — monta payload da LLM (system prompt substituído + histórico + cardápio + promoções)
- [ ] HTTP Request — chama OpenAI (gpt-4o-mini)
- [ ] Edit Fields — extrai resposta da LLM
- [ ] Supabase — Insert mensagens (role: assistant)
- [ ] HTTP Request — Evolution API envia resposta ao cliente
- [ ] Implementar mecanismo de atendimento humano (detectar sinalização da Sofia, notificar estabelecimento, pausar fluxo)

### Pré-requisitos antes de montar o payload

- [ ] Definir formatação do cardápio para injeção no prompt
- [ ] Definir formatação das promoções — string vazia ou mensagem neutra quando não houver ativas
- [ ] Alinhar nomenclatura dos campos dinâmicos com colunas do Supabase

### Estado atual do fluxo

```
Webhook
  ↓
Split Out
  ↓
IFfromMe
  ├── false → No Operation
  └── true ↓
Edit Fields — extracaoTelefone (TelefoneC + TelefoneE)   ✓
  ↓
Supabase — busca estabelecimento por TelefoneE            ✓
  ↓
HTTP Request — upsert contatos                            ✓
  ↓
Supabase — busca contato (retorna contato_id)             ✓
  ↓
Supabase — Insert mensagens (role: user)                  ✓
  ↓
Supabase — Select mensagens (histórico)                   ✓
  ↓
Supabase — Select cardápio (Execute Once)                 ✓
  ↓
Supabase — Select promoções (Execute Once)                ✓
  ↓
[próximos nós — ver lista acima]
```

---

## Supabase — Segurança

- [ ] Reativar RLS com políticas corretas (desabilitado temporariamente durante debug)

