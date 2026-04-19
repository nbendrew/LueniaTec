# Em Andamento
Última atualização: 09/04/2026

---

## Infraestrutura / VPS

- [x] Webhook da instância wa4 atualizado para IP gateway `172.18.0.1` — concluído 19/04/2026
- [x] N8N conectado à rede `evolution-net` — configuração persistida no docker-compose

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

## Bugs Identificados

- [ ] **Pizza meia a meia retorna preço inteiro de cada sabor**
  Comportamento atual: Sofia interpreta dois sabores como dois pedidos separados, cobrando o valor inteiro de cada um.
  Comportamento esperado: reconhecer "meia calabresa e meia 4 queijos" como uma única pizza com preço de uma pizza inteira.
  Onde corrigir: system prompt da Sofia — adicionar regra explícita de tratamento de pizza meia a meia.

---

## Supabase — Pendências

- [ ] Adicionar coluna `endereco` (text, nullable) na tabela `estabelecimentos`
- [ ] Reativar RLS com políticas corretas (desabilitado temporariamente durante debug)

