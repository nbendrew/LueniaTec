# Em Andamento
Última atualização: 04/04/2026

---

## N8N — Fechamento do Fluxo Principal

### Pendências imediatas (próxima sessão)

- [ ] Ativar `Execute Once` no nó **Select cardápio**
- [ ] Ativar `Execute Once` no nó **Select promoções**
- [ ] Testar Select promoções (ainda não testado)
- [ ] Montar nó `Edit Fields` consolidando payload da LLM (system prompt + histórico + cardápio + promoções)
- [ ] Configurar `Aggregate` para formatar o histórico no padrão `[{role, content}]`
- [ ] HTTP Request — chamar LLM (OpenAI gpt-4o-mini recomendado para teste inicial)
- [ ] Nó para extrair resposta da LLM
- [ ] Supabase — Insert mensagens (role: assistant)
- [ ] HTTP Request — Evolution API enviar resposta ao cliente

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
Supabase — Get contato (buscar contato_id)                ✓
  ↓
Supabase — Insert mensagens (role: user)                  ✓
  ↓
Supabase — Select histórico mensagens                     ✓
  ↓
Supabase — Select cardápio                                ✓ (Execute Once pendente)
  ↓
Supabase — Select promoções                               ⚠ (não testado)
  ↓
[próximos nós a construir — ver lista acima]
```

---

## Supabase — Segurança

- [ ] Reativar RLS em todas as tabelas com políticas corretas (desabilitado temporariamente durante debug)

