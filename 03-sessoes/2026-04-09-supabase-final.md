# Sessão — 09/04/2026
**Tema:** Supabase — Estado Final do Banco
**Participantes:** Sócios

---

## O que foi feito

Sessão de consolidação e validação do estado final do banco de dados antes de ir para produção.

### Decisões confirmadas
- Multi-tenant com `estabelecimento_id` em todas as tabelas
- Supabase Cloud plano Free — VPS sem RAM suficiente para self-hosted
- Cardápio: uma linha por variação (ex: Calabresa M, Calabresa G) — tamanhos como registros separados
- Campo `itens` em pedidos usando JSONB — flexível para o MVP
- Tabela `contatos` usa `UNIQUE(estabelecimento_id, telefone)`

### Tabelas criadas e validadas — estado final

| Tabela           | RLS   | Observação                                      |
|------------------|-------|-------------------------------------------------|
| estabelecimentos | ativo |                                                 |
| contatos         | ativo |                                                 |
| mensagens        | ativo | FK `contato_id` declarada formalmente           |
| cardapio         | ativo |                                                 |
| promocoes        | ativo |                                                 |
| pedidos          | ativo | campo `itens` em JSONB                          |
| configuracoes    | ativo | inclui campo `observacoes`                      |

### Dados de teste inseridos
- Estabelecimento: `Teste_Estabelecimento` — ativo
- Cardápio com variações M e G: Calabresa, 4 Queijos, Hamburguer Clássico, Coca-Cola Lata
- Registro em `configuracoes` vinculado ao `Teste_Estabelecimento`

### Acesso via MCP
- Claude acessa o banco diretamente via MCP do Supabase
- Consegue cadastrar estabelecimentos, inserir cardápio e gerenciar dados direto no chat
- Fluxo para novo cliente: passar os dados e cardápio → Claude cadastra tudo

---

## Pendências que ficaram

- [ ] Reativar RLS com políticas corretas antes de produção

---

## Próximos passos

1. Chat N8N — testar fluxo de ponta a ponta com mensagem real pelo WhatsApp
