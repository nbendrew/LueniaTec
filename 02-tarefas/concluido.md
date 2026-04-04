# Concluído
Última atualização: 04/04/2026

---

## Infraestrutura

- [x] VPS Hostinger KVM 1 provisionada (Ubuntu 24.04, IP 187.127.7.78)
- [x] PostgreSQL 15 rodando via Docker em `/opt/evolution/postgres/`
- [x] Redis Alpine rodando via Docker em `/opt/evolution/redis/`
- [x] Evolution API v2.3.7 rodando via Docker em `/opt/evolution/api/`
- [x] N8N instalado e rodando via Docker em `/opt/n8n/` (porta 5678)
- [x] N8N acessível via HTTP com `N8N_SECURE_COOKIE=false`

---

## Evolution API

- [x] Instância `wa4` criada e WhatsApp conectado
- [x] Instância `wa5` criada e WhatsApp conectado
- [x] Webhook configurado em `wa4` — evento `MESSAGES_UPSERT`, apontando para `http://187.127.7.78:5678/webhook/evoapi`
- [x] Webhook configurado em `wa5` — mesma configuração
- [x] Payload mapeado (remoteJid, fromMe, pushName, conversation, messageType)

---

## Supabase

- [x] Projeto criado na região São Paulo (`https://wyngopoqjvegqpvttjxb.supabase.co`)
- [x] Tabela `estabelecimentos` criada (com registro de teste)
- [x] Tabela `contatos` criada
- [x] Tabela `mensagens` criada
- [x] Tabela `cardapio` criada (com 4 itens de teste)
- [x] Tabela `promocoes` criada
- [x] Tabela `pedidos` criada
- [x] RLS habilitado em todas as tabelas

---

## N8N — Fluxo Principal

- [x] Webhook recebendo payload da Evolution API
- [x] Split Out configurado
- [x] Filtro `IFfromMe` — descarta mensagens do próprio bot
- [x] Extração de `TelefoneC` (cliente) e `TelefoneE` (estabelecimento)
- [x] Busca dinâmica do estabelecimento por `telefone_whatsapp`
- [x] Upsert de contatos com `Continue On Fail` (trata duplicatas sem quebrar o fluxo)
- [x] Get contato para obter `contato_id`
- [x] Insert mensagens (role: user)
- [x] Select histórico de mensagens (filtro `contato_id` + `estabelecimento_id`, ordenado por `criado_em ASC`)
- [x] Select cardápio retornando itens corretamente

---

## Problemas Resolvidos

- [x] Dashboard Evolution API travando — contornado via curl direto na API
- [x] Erro 401 Unauthorized no webhook — corrigido com header `apikey` via curl
- [x] URL do webhook apontava para N8N cloud — corrigida para VPS
- [x] Fluxo N8N inativo — resolvido publicando via botão Publish
- [x] Credencial Supabase com chave errada (`sb_secret`) — substituída pela `service_role`
- [x] UUID do estabelecimento hardcoded — agora buscado dinamicamente pelo TelefoneE
- [x] `\n` invisível no campo `telefone_whatsapp` causava falha no filtro — identificado e corrigido
- [x] Itens duplicados no Select cardápio — solução: `Execute Once` (a implementar)

