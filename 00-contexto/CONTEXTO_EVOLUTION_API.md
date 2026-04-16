================================================================================
CONTEXTO DO CHAT — EVOLUTION API
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

Este chat cobre tudo relacionado à Evolution API:
- Configuração e manutenção da instância
- Webhooks (configuração, eventos, payloads)
- Envio e recebimento de mensagens
- Tratamento de mídia (áudios, imagens, documentos)
- Troubleshooting da integração com WhatsApp


================================================================================
STATUS ATUAL DA EVOLUTION API
================================================================================

Versão:       v2.3.7 (imagem: evoapicloud/evolution-api:v2.3.7)
Container:    rodando via Docker em /opt/evolution/api/
Porta:        8080
URL base:     http://187.127.7.78:8080
Dashboard:    http://187.127.7.78:8080/manager
API Key:      tolokodedroga
Instância:    wa4 (WhatsApp conectado e funcionando)


================================================================================
DECISÕES TÉCNICAS RELEVANTES
================================================================================

- A Evolution API é o ponto de entrada de todas as mensagens do WhatsApp
- Cada mensagem recebida dispara um webhook para o N8N
- O N8N é responsável por orquestrar o que acontece após o recebimento
- A Evolution API também é usada para enviar as respostas ao cliente


================================================================================
PENDÊNCIAS / PRÓXIMOS PASSOS NESTE TEMA
================================================================================

- Configurar webhook apontando para o N8N
- Definir quais eventos do webhook serão utilizados
- Testar recebimento de mensagens de texto
- Planejar tratamento de áudios enviados pelo cliente


================================================================================
HISTÓRICO DE DECISÕES E APRENDIZADOS
================================================================================

Evento MESSAGES_UPSERT configurado no webhook — cobre texto e áudio sem necessidade de alteração futura.
Webhook apontando para o N8N testado com sucesso na instância wa4.
Payload recebido corretamente com todos os campos esperados.
================================================================================
Resumo da sessão — Evolution API

Webhook configurado na instância wa4

Evento: MESSAGES_UPSERT
URL final: http://187.127.7.78:5678/webhook/evoapi
Status: ativo e funcionando

Webhook configurado na instância wa5

Mesma configuração da wa4
Testado e funcionando


Problemas resolvidos:

Dashboard da Evolution API travando — contornado gerenciando tudo via curl direto na API
Erro Unauthorized — causado por tentativa de configuração via dashboard, resolvido via curl com header apikey
Erro Bad Request — body do endpoint v2 exige campos webhook e enabled explícitos
URL errada no webhook — apontava para o N8N cloud, corrigida para o N8N da VPS
Fluxo N8N inativo — URL de produção só responde com fluxo publicado via botão Publish


Padrão estabelecido para novas instâncias:
Qualquer nova instância criada na Evolution API segue o mesmo curl, substituindo apenas o nome da instância:
bashcurl -X POST http://187.127.7.78:8080/webhook/set/NOME_INSTANCIA \
  -H "apikey: tolokodedroga" \
  -H "Content-Type: application/json" \
  -d '{
    "webhook": {
      "enabled": true,
      "url": "http://srv1546072.hstgr.cloud:5678/webhook/evoapi",
      "webhook_by_events": false,
      "webhook_base64": false,
      "events": ["MESSAGES_UPSERT"]
    }
  }'
  ================================================================================
