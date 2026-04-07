# Em Andamento
Última atualização: 07/04/2026

---

## N8N — Fechamento do Fluxo Principal

### Bloqueador atual
Antes de montar o payload da LLM, é necessário definir o system prompt no chat do agente.
**Próxima ação: Chat LLM/Agente → definir system prompt base.**

### Próximos nós a construir (em ordem)

- [ ] Edit Fields — monta payload da LLM (system prompt + histórico + cardápio + promoções)
- [ ] HTTP Request — chama OpenAI
- [ ] Edit Fields — extrai resposta da LLM
- [ ] Supabase — Insert mensagens (role: assistant)
- [ ] HTTP Request — Evolution API envia resposta ao cliente

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

