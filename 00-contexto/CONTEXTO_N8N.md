================================================================================
CONTEXTO DO CHAT — N8N
================================================================================
Última atualização: 07/04/2026
Versão: 1.1

AVISO IMPORTANTE
----------------
Este arquivo é atualizado regularmente conforme o projeto evolui.
Sempre considere que este arquivo pode conter informações mais recentes
do que o CONTEXTO_GERAL_PROJETO.txt.
Leia também o CONTEXTO_GERAL_PROJETO.txt para o contexto completo do projeto.


================================================================================
ESCOPO DESTE CHAT
================================================================================

Este chat cobre tudo relacionado ao N8N:
- Instalação e configuração na VPS
- Criação e manutenção de fluxos
- Lógica de orquestração entre Evolution API, Supabase e LLM
- Gerenciamento de sessão por número de telefone
- Tratamento de erros e casos extremos


================================================================================
STATUS ATUAL DO N8N
================================================================================

Instalação:     concluída
Container:      rodando via Docker em /opt/n8n/
Porta:          5678
URL:            http://187.127.7.78:5678
Usuário admin:  criado
Status:         acessível e funcionando

Webhook:        path `evoapi`, recebendo payload da Evolution API
Fluxo:          em construção — primeira metade concluída


================================================================================
INSTALAÇÃO
================================================================================

Pasta:    /opt/n8n/
Porta:    5678
URL:      http://187.127.7.78:5678
Método:   Docker Compose

Observação: `N8N_SECURE_COOKIE=false` adicionado ao docker-compose para
permitir acesso via HTTP (IP direto, sem HTTPS).
Revisitar quando SSL for configurado.


================================================================================
PAYLOAD DO WEBHOOK — CAMPOS MAPEADOS
================================================================================

body.data.key.remoteJid   → identificador do cliente
body.data.key.fromMe      → filtra mensagens do próprio bot
body.data.pushName        → nome do contato
body.data.message.conversation → texto da mensagem
body.data.messageType     → tipo da mensagem
body.sender               → telefone do estabelecimento (split('@')[0])


================================================================================
FLUXO PRINCIPAL — ESTADO ATUAL
================================================================================

Como o N8N funciona neste projeto:
- O fluxo é ativado a cada mensagem recebida via webhook da Evolution API
- O fluxo NÃO fica aberto esperando — ele morre e renasce a cada mensagem
- A memória da conversa é mantida pelo Supabase, não pelo N8N

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
[próximos nós a construir — ver seção abaixo]
```

Detalhes dos nós implementados:

extracaoTelefone (Edit Fields)
- TelefoneC → cliente: `body.data.key.remoteJid` split('@')[0]
- TelefoneE → estabelecimento: `body.sender` split('@')[0]

Upsert contatos (HTTP Request)
- Continue On Fail ativado — trata erro 409 sem quebrar o fluxo
- Body: estabelecimento_id (dinâmico) + telefone (TelefoneC) + nome (pushName)

Busca estabelecimento (Supabase Get Many)
- Filtro: telefone_whatsapp = TelefoneE

Busca contato (Supabase Get)
- Filtro: telefone + estabelecimento_id (Must Match: All Filters)
- Retorna contato_id usado no Insert de mensagens

Select mensagens (histórico)
- Filtro: contato_id + estabelecimento_id (Must Match: All Filters)
- Ordenado por criado_em ASC

Select cardápio e Select promoções
- Execute Once ativado em ambos — evita duplicação de resultados
- Filtro: estabelecimento_id + ativo: true


================================================================================
PRÓXIMOS NÓS A CONSTRUIR
================================================================================

Bloqueador: definir system prompt no chat do agente antes de montar o payload.

1. Edit Fields — monta payload da LLM (system prompt + histórico + cardápio + promoções)
2. HTTP Request — chama OpenAI
3. Edit Fields — extrai resposta da LLM
4. Supabase — Insert mensagens (role: assistant)
5. HTTP Request — Evolution API envia resposta ao cliente


================================================================================
DESAFIOS TÉCNICOS MAPEADOS
================================================================================

- Tratamento de áudios (transcrição antes de passar para a LLM)
- Pedidos ambíguos ou incompletos
- Múltiplos itens na mesma mensagem
- Mudança de pedido no meio da conversa
- Endereços incompletos
- Tratamento de erros no fluxo (LLM indisponível, Supabase falhar, etc.)


================================================================================
PENDÊNCIAS / PRÓXIMOS PASSOS NESTE TEMA
================================================================================

- Definir system prompt no chat LLM/Agente
- Montar Edit Fields com payload completo da LLM
- Fechar os 5 nós restantes e testar fluxo de ponta a ponta
- Reativar RLS no Supabase com políticas corretas antes de produção


================================================================================
HISTÓRICO DE DECISÕES E APRENDIZADOS
================================================================================

N8N_SECURE_COOKIE=false — necessário para acesso via HTTP sem HTTPS.
  Revisitar quando SSL for configurado.

Execute Once — obrigatório nos nós Select cardápio e Select promoções.
  Sem isso, os nós executam uma vez para cada item do histórico, gerando duplicatas.

Credencial Supabase — usar chave service_role, não sb_secret.

UUID do estabelecimento — nunca hardcoded. Sempre buscado dinamicamente pelo TelefoneE.

\n invisível — campo telefone_whatsapp no Supabase continha caractere invisível
  causando falha no filtro. Identificado e corrigido.

contato_id — a tabela mensagens não tem campo telefone, apenas contato_id.
  É obrigatório buscar o contato antes do Insert de mensagens.

================================================================================
