================================================================================
CONTEXTO DO CHAT — SUPABASE
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

Este chat cobre tudo relacionado ao Supabase:
- Criação e estrutura das tabelas
- Queries utilizadas pelo N8N
- Gerenciamento de histórico de mensagens
- Cardápio e promoções dinâmicas
- Registro de pedidos


================================================================================
STATUS ATUAL DO SUPABASE
================================================================================

Conta criada: sim (presumido)
Tabelas criadas: nenhuma ainda
Próximo passo: criar tabelas iniciais (contatos e mensagens)


================================================================================
ESTRUTURA DO BANCO DE DADOS PLANEJADA
================================================================================

--- Tabela: contatos ---
Objetivo: registrar cada cliente que entra em contato

| coluna     | tipo      | detalhe                        |
|------------|-----------|--------------------------------|
| telefone   | text      | chave primária                 |
| nome       | text      | nome do contato                |
| criado_em  | timestamp | primeira vez que entrou em contato |


--- Tabela: mensagens ---
Objetivo: armazenar o histórico completo de cada conversa

| coluna     | tipo      | detalhe                                      |
|------------|-----------|----------------------------------------------|
| id         | uuid      | chave primária (gerado automaticamente)      |
| telefone   | text      | chave estrangeira → contatos                 |
| role       | text      | "user" ou "assistant"                        |
| conteudo   | text      | texto da mensagem                            |
| criado_em  | timestamp | usado para ordenar a conversa cronologicamente |

Observação sobre o campo `role`:
O histórico enviado para a LLM exige o formato:
[
  {"role": "user", "content": "oi, quero uma pizza"},
  {"role": "assistant", "content": "Olá! Qual sabor você deseja?"},
  {"role": "user", "content": "calabresa"}
]
Sem este campo, a IA não distingue o que foi dito pelo cliente
do que foi dito por ela mesma.


--- Tabelas futuras planejadas ---

| tabela     | objetivo                                         |
|------------|--------------------------------------------------|
| cardapio   | itens disponíveis, consultados dinamicamente     |
| promocoes  | promoções com campo `ativo` (true/false)         |
| pedidos    | registro dos pedidos finalizados                 |


================================================================================
LÓGICA DE PROMOÇÕES DINÂMICAS
================================================================================

- Tabela `promocoes` terá campo `ativo` (boolean)
- O N8N consulta esta tabela antes de cada chamada à LLM
- Promoções ativas são injetadas dinamicamente no system prompt
- MVP: dono ativa/desativa via comando pelo WhatsApp
- Futuro: painel web para gerenciamento


================================================================================
PENDÊNCIAS / PRÓXIMOS PASSOS NESTE TEMA
================================================================================

- Criar tabela `contatos` no Supabase
- Criar tabela `mensagens` no Supabase
- Obter URL e chave de API do Supabase para integrar com N8N
- Testar insert e select via N8N
- Planejar estrutura das tabelas `cardapio` e `promocoes`


================================================================================
HISTÓRICO DE DECISÕES E APRENDIZADOS
================================================================================

Decisões tomadas:

Arquitetura multi-tenant com estabelecimento_id em todas as tabelas
Supabase Cloud (plano Free) — VPS não tem RAM suficiente para self-hosted
Tamanhos e adicionais no cardápio adiados para depois do MVP
Campo itens em pedidos usando JSONB — flexível para o MVP

Infraestrutura:

Projeto criado na região São Paulo
URL: https://wyngopoqjvegqpvttjxb.supabase.co

Tabelas criadas:

✓ estabelecimentos — com registro de teste inserido
✓ contatos
✓ mensagens
✓ cardapio — com 4 itens de teste
✓ promocoes
✓ pedidos
✓ RLS habilitado em todas

Próximo passo:

Chat N8N — configurar credenciais do Supabase e testar as queries do fluxo

================================================================================
