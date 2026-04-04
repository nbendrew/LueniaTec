================================================================================
CONTEXTO DO CHAT — N8N
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

Este chat cobre tudo relacionado ao N8N:
- Instalação e configuração na VPS
- Criação e manutenção de fluxos
- Lógica de orquestração entre Evolution API, Supabase e LLM
- Gerenciamento de sessão por número de telefone
- Tratamento de erros e casos extremos


================================================================================
STATUS ATUAL DO N8N
================================================================================

Instalação: ainda não realizada
Próximo passo: instalar via Docker em /opt/n8n/


================================================================================
INSTALAÇÃO PLANEJADA
================================================================================

Pasta:    /opt/n8n/
Porta:    5678
URL:      http://187.127.7.78:5678
Método:   Docker Compose

docker-compose.yaml planejado:

  version: '3.8'
  services:
    n8n:
      image: n8nio/n8n:latest
      container_name: n8n
      restart: always
      ports:
        - "5678:5678"
      environment:
        - N8N_HOST=187.127.7.78
        - N8N_PORT=5678
        - N8N_PROTOCOL=http
        - WEBHOOK_URL=http://187.127.7.78:5678/
        - GENERIC_TIMEZONE=America/Sao_Paulo
        - TZ=America/Sao_Paulo
      volumes:
        - n8n_data:/home/node/.n8n
  volumes:
    n8n_data:


================================================================================
LÓGICA DO FLUXO PRINCIPAL (PLANEJADA)
================================================================================

Como o N8N funciona neste projeto:
- O fluxo é ativado a cada mensagem recebida via webhook da Evolution API
- O fluxo NÃO fica aberto esperando — ele morre e renasce a cada mensagem
- A memória da conversa é mantida pelo Supabase, não pelo N8N

Sequência do fluxo:
1. Webhook recebe mensagem da Evolution API
2. Identifica o número de telefone do remetente
3. Busca histórico da conversa no Supabase (tabela mensagens)
4. Busca cardápio ativo no Supabase
5. Busca promoções ativas no Supabase
6. Monta o payload para a LLM (system prompt + histórico + mensagem nova)
7. Chama a LLM e obtém resposta
8. Salva mensagem do user no Supabase (role: "user")
9. Salva resposta da IA no Supabase (role: "assistant")
10. Envia resposta ao cliente via Evolution API


================================================================================
DESAFIOS TÉCNICOS MAPEADOS PARA ESTE CHAT
================================================================================

- Gerenciamento de sessão por número de telefone
- Tratamento de áudios (transcrição antes de passar para a LLM)
- Pedidos ambíguos ou incompletos
- Múltiplos itens na mesma mensagem
- Mudança de pedido no meio da conversa
- Endereços incompletos


================================================================================
PENDÊNCIAS / PRÓXIMOS PASSOS NESTE TEMA
================================================================================

- Instalar N8N na VPS via Docker
- Acessar interface e criar usuário admin
- Configurar credenciais (Supabase, Evolution API, LLM)
- Criar primeiro fluxo de teste (receber mensagem e responder)
- Evoluir para o fluxo completo com memória


================================================================================
HISTÓRICO DE DECISÕES E APRENDIZADOS
================================================================================
Atualizar o bloco de status:
Instalação: concluída
Container: rodando via Docker em /opt/n8n/
Porta: 5678
URL: http://187.127.7.78:5678
Usuário admin: criado
Status: acessível e funcionando
Adicionar ao bloco de histórico de decisões:
- N8N_SECURE_COOKIE=false adicionado ao docker-compose para permitir
  acesso via HTTP (IP direto, sem HTTPS). Revisitar quando SSL for configurado.
Atualizar pendências — marcar como concluído:
✓ Instalar N8N na VPS via Docker
✓ Acessar interface e criar usuário admin
================================================================================
Webhook criado com path evoapi em luenia.app.n8n.cloud.
Mapeados os campos úteis do payload:

body.data.key.remoteJid → identificador do cliente
body.data.key.fromMe → filtra mensagens do bot
body.data.pushName → nome do contato
body.data.message.conversation → texto da mensagem
body.data.messageType → tipo da mensagem


Nó If configurado: fromMe = false no True branch, False branch descarta via No Operation.
================================================================================
RESUMO — SESSÃO ATUAL (03/04/2026)

AVANÇOS REALIZADOS
extracaoTelefone (Edit Fields)

Campo renomeado: Telefone → TelefoneC (cliente)
Campo adicionado: TelefoneE (estabelecimento) → {{ $json['body.sender'].split('@')[0] }}

Upsert contatos (HTTP Request)

Continue On Fail ativado — trata erro 409 (duplicate key) sem quebrar o fluxo
Body funcionando:

json{
  "estabelecimento_id": "{{ id dinâmico }}",
  "telefone": "{{ $('extracaoTelefone').item.json['TelefoneC'] }}",
  "nome": "{{ $('IFfromMe').item.json['body.data.pushName'] }}"
}
Busca estabelecimento (Supabase Get Many ou HTTP Request)

Filtra por telefone_whatsapp = TelefoneE
Retorna id do estabelecimento dinamicamente
Problema resolvido: \n invisível no campo telefone_whatsapp causava falha no filtro


PROBLEMAS RESOLVIDOS

Credencial Supabase estava com chave errada (sb_secret_...) — substituída pela service_role correta
RLS desabilitado temporariamente durante debug — verificar se precisa reativar com políticas
UUID do estabelecimento era hardcoded — agora buscado dinamicamente pelo TelefoneE
Campo contato_id na tabela mensagens exige buscar o id do contato antes do insert


PENDÊNCIAS IMEDIATAS

Atualizar o body do upsert contatos para usar $json.id do nó de busca do estabelecimento
Resolver o contato_id no Insert de mensagens — tabela mensagens não tem campo telefone, tem contato_id
Verificar se RLS precisa ser reativado com políticas corretas


FLUXO ATUAL
Webhook
  ↓
Split Out — classificar e filtrar
  ↓
IFfromMe
  ├── false → No Operation
  └── true ↓
Edit Fields — extracaoTelefone (TelefoneC + TelefoneE)
  ↓
Supabase/HTTP — busca estabelecimento por TelefoneE
  ↓
HTTP Request — upsert contatos (em ajuste)
  ↓
(próximos nós a construir)

PRÓXIMOS NÓS A CONSTRUIR

Supabase — Get contato (buscar contato_id)
Supabase — Insert mensagens (role: user)
Supabase — Select mensagens (histórico)
Supabase — Select cardapio
Supabase — Select promocoes
Edit Fields — monta payload LLM
HTTP Request — chama LLM
Supabase — Insert mensagens (role: assistant)
HTTP Request — Evolution API (envia resposta)
================================================================================
RESUMO — SESSÃO 03/04/2026 (tarde)

AVANÇOS REALIZADOS
Select histórico mensagens

Funcionando corretamente
Filtros: contato_id + estabelecimento_id com Must Match: All Filters
Ordenado por criado_em ASC

Select cardapio

Retornando os itens corretamente
Problema identificado: itens duplicados porque o nó executa uma vez para cada item do histórico
Solução definida: ativar Execute Once nas Settings do nó

Select promocoes

Ainda não testado


PROBLEMAS IDENTIFICADOS

Nós Select cardapio e Select promocoes precisam de Execute Once ativado para não duplicar resultados
Aggregate testado mas travou — descartado por enquanto, será usado mais à frente na montagem do payload da LLM


PENDÊNCIAS IMEDIATAS

Ativar Execute Once no Select cardapio e Select promocoes
Testar Select promocoes
Montar nó Edit Fields para consolidar payload da LLM
Configurar Aggregate para o histórico de mensagens na montagem do payload


PRÓXIMOS NÓS A CONSTRUIR

Edit Fields — monta payload da LLM (system prompt + histórico + cardapio + promocoes)
HTTP Request — chama LLM
Edit Fields — extrai resposta da LLM
Supabase — Insert mensagens (role: assistant)
HTTP Request — Evolution API (envia resposta ao cliente)
================================================================================
