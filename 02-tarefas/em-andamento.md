# Em Andamento
Última atualização: 08/04/2026

---

## N8N — Ajustes Finais e Teste

O fluxo está completo de ponta a ponta. Faltam ajustes pontuais e o teste real.

- [ ] Corrigir `=` no início do conteúdo salvo no Supabase — problema no modo do campo no nó extrairResposta (Edit Fields)
- [ ] Limpar tabela `mensagens` no Supabase — dados sujos de testes acumulados
- [ ] Testar fluxo de ponta a ponta com mensagem real chegando pelo WhatsApp
- [ ] Tornar instância dinâmica na URL da Evolution API (hardcoded `wa4` por ora)

### Fluxo completo — todos os nós

```
Webhook → Split Out → IFfromMe
  ↓
extracaoTelefone                                  ✓
coletar_id_estabelecimentos                       ✓
registrarContato (upsert)                         ✓
coletar_id_contatos                               ✓
registrar_mensagens (Insert user)                 ✓
historico_mensagens (Select)                      ✓
cardapio (Select — Execute Once)                  ✓
promocoes (Select — Execute Once)                 ✓
Get a row (Select configuracoes)                  ✓
Code in JavaScript (monta payload)                ✓
HTTP Request (OpenAI gpt-4o-mini)                 ✓
extrairResposta (Edit Fields)                     ⚠ corrigir `=`
Supabase Insert (assistant)                       ✓
HTTP Request (Evolution API)                      ✓
```

---

## Supabase — Segurança

- [ ] Reativar RLS com políticas corretas (desabilitado temporariamente durante debug)

