================================================================================
CONTEXTO DO CHAT — N8N
================================================================================
Última atualização: 09/04/2026
Versão: 1.3

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
Fluxo:          completo — todos os nós construídos, aguardando teste de ponta a ponta


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
classificar e filtrar (Split Out)
  ↓
IFfromMe
  ├── false → No Operation
  └── true ↓
extracaoTelefone (TelefoneC + TelefoneE)              ✓
  ↓
coletar_id_estabelecimentos                            ✓
  ↓
registrarContato (HTTP Request — upsert)               ✓
  ↓
coletar_id_contatos                                    ✓
  ↓
registrar_mensagens (Insert user)                      ✓
  ↓
historico_mensagens (Select)                           ✓
  ↓
cardapio (Select — Execute Once)                       ✓
  ↓
promocoes (Select — Execute Once)                      ✓
  ↓
Get a row (Select configuracoes)                       ✓
  ↓
Code in JavaScript (monta payload)                     ✓
  ↓
HTTP Request (OpenAI gpt-4o-mini)                      ✓
  ↓
extrairResposta (Edit Fields)                          ⚠ corrigir `=`
  ↓
Supabase Insert (assistant)                            ✓
  ↓
HTTP Request (Evolution API — envia ao cliente)        ✓
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

Code in JavaScript (payload LLM)
- Monta system prompt com variáveis substituídas dinamicamente
- Formata cardápio: `- Nome: R$preco — descricao`
- Formata promoções: filtra apenas ativo: true
- Monta histórico no formato [{role, content}]
- Retorna body serializado via JSON.stringify (contorna limitação do N8N com arrays)

HTTP Request (OpenAI)
- Modelo: gpt-4o-mini
- Body Content Type: Raw com Content-Type: application/json
- Body: `={{ $json.body }}`

HTTP Request (Evolution API)
- URL: `http://187.127.7.78:8080/message/sendText/wa4`
- Body: JSON.stringify para contornar quebras de linha e markdown na resposta
- Obs: instância `wa4` hardcoded — tornar dinâmica futuramente


================================================================================
PRÓXIMOS NÓS A CONSTRUIR
================================================================================

Fluxo completo. Pendências restantes:

- Corrigir `=` no início do conteúdo salvo — problema no modo do campo no nó extrairResposta
- Limpar tabela `mensagens` no Supabase (dados sujos de testes)
- Testar fluxo de ponta a ponta com mensagem real chegando pelo WhatsApp
- Tornar instância dinâmica na URL da Evolution API (hardcoded `wa4` por ora)
- Implementar mecanismo de atendimento humano (detectar sinalização da Sofia, notificar estabelecimento, pausar fluxo)


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

- Definir formatação do cardápio para injeção no prompt (definido: lista simples, uma linha por item)
- Montar nó Select configuracoes no N8N
- Montar Edit Fields com payload completo da LLM
- Fechar os nós restantes e testar fluxo de ponta a ponta
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

Formatação do cardápio — lista simples, uma linha por item.
  Exemplo: "- Pizza Calabresa (G): R$45,00"
  Concatenada em string antes de injetar na variável {cardapio} do system prompt.

Serialização do payload — JSON.stringify obrigatório no nó Code.
  N8N não passa arrays corretamente para HTTP Request sem serialização manual.

Quebras de linha na resposta da Sofia — JSON.stringify na expressão do body da Evolution API.
  Resolve problema de markdown quebrando o payload JSON.

================================================================================
