================================================================================
CONTEXTO DO CHAT — INFRAESTRUTURA / VPS
================================================================================
Última atualização: 09/04/2026
Versão: 1.2

AVISO IMPORTANTE
----------------
Este arquivo é atualizado regularmente conforme o projeto evolui.
Sempre considere que este arquivo pode conter informações mais recentes
do que o CONTEXTO_GERAL_PROJETO.txt.
Leia também o CONTEXTO_GERAL_PROJETO.txt para o contexto completo do projeto.


================================================================================
ESCOPO DESTE CHAT
================================================================================

Este chat cobre tudo relacionado à infraestrutura:
- Configuração e manutenção da VPS
- Gerenciamento dos containers Docker
- Segurança (firewall, portas, autenticação)
- SSL/HTTPS (futuro)
- Domínio (futuro)
- Monitoramento e logs


================================================================================
CONFIGURAÇÃO ATUAL DA VPS
================================================================================

Provedor:   Hostinger, plano KVM 1
IP:         187.127.7.78
Domínio:    srv1546072.hstgr.cloud (hostname padrão Hostinger)
OS:         Ubuntu 24.04 LTS
Acesso:     ssh root@187.127.7.78


================================================================================
CONTAINERS DOCKER EM EXECUÇÃO
================================================================================

Todos os containers ficam em /opt/evolution/

| serviço         | imagem                              | porta | pasta                    |
|-----------------|-------------------------------------|-------|--------------------------|
| PostgreSQL 15   | postgres:15                         | 5432  | /opt/evolution/postgres/ |
| Redis Alpine    | redis:alpine                        | 6379  | /opt/evolution/redis/    |
| Evolution API   | evoapicloud/evolution-api:v2.3.7    | 8080  | /opt/evolution/api/      |
| N8N             | n8nio/n8n:latest                    | 5678  | /opt/n8n/                |



================================================================================
ESTRUTURA DE ARQUIVOS NA VPS
================================================================================

/opt/evolution/
├── postgres/
│   └── docker-compose.yaml
├── redis/
│   └── docker-compose.yaml
└── api/
    ├── docker-compose.yaml
    └── .env

/opt/n8n/
└── docker-compose.yaml


================================================================================
CONFIGURAÇÃO DO N8N (docker-compose.yaml)
================================================================================

Variáveis de ambiente obrigatórias:

  N8N_EDITOR_BASE_URL=http://srv1546072.hstgr.cloud:5678
  WEBHOOK_URL=http://srv1546072.hstgr.cloud:5678
  N8N_SECURE_COOKIE=false

Motivo:
- N8N_EDITOR_BASE_URL e WEBHOOK_URL: necessárias para que o N8N gere URLs
  corretas (webhooks, OAuth callbacks) usando o domínio em vez do IP.
  Sem isso, o N8N usa o hostname detectado automaticamente (IP),
  causando falha no OAuth — Google e outros provedores não aceitam IP como redirect URI.
- N8N_SECURE_COOKIE=false: necessário para acesso via HTTP sem HTTPS.
  Revisitar e remover quando SSL for configurado.

Recriar o container após qualquer alteração no docker-compose.yaml:
  cd /opt/n8n && docker compose down && docker compose up -d


================================================================================
CREDENCIAIS E ACESSOS
================================================================================

Evolution API Key:  tolokodedroga
Evolution URL:      http://187.127.7.78:8080
Evolution Manager:  http://187.127.7.78:8080/manager
N8N URL:            http://srv1546072.hstgr.cloud:5678
Webhook path:       http://172.18.0.1:5678/webhook/evoapi (IP gateway da rede evolution-net — uso interno entre containers)
N8N URL pública:    http://srv1546072.hstgr.cloud:5678


================================================================================
WEBHOOK — PADRÃO PARA INSTÂNCIAS EVOLUTION API
================================================================================

Qualquer nova instância criada na Evolution API segue este curl,
substituindo apenas o nome da instância:

curl -X POST http://187.127.7.78:8080/webhook/set/NOME_INSTANCIA \
  -H "apikey: tolokodedroga" \
  -H "Content-Type: application/json" \
  -d '{
    "webhook": {
      "enabled": true,
      "url": "http://172.18.0.1:5678/webhook/evoapi",
      "webhook_by_events": false,
      "webhook_base64": false,
      "events": ["MESSAGES_UPSERT"]
    }
  }'

Instâncias ativas: wa4 (conectada ao WhatsApp)
Instância wa5: excluída em 09/04/2026 — sem relevância para o projeto


================================================================================
PENDÊNCIAS / PRÓXIMOS PASSOS NESTE TEMA
================================================================================

- [x] Webhook da instância wa4 atualizado para IP gateway (172.18.0.1) — concluído 19/04/2026
- [ ] Configurar firewall — fechar portas desnecessariamente expostas
- [ ] Configurar SSL/HTTPS com Nginx + Certbot (Let's Encrypt)
- [ ] Após SSL: atualizar variáveis N8N para HTTPS, remover N8N_SECURE_COOKIE=false,
      fechar porta 5678 no firewall (acesso só via Nginx na 443)
- [ ] Considerar domínio próprio para produção (srv1546072.hstgr.cloud é hostname padrão Hostinger)
- [ ] Configurar monitoramento básico dos containers (uptime, restart automático)


================================================================================
ROADMAP HTTPS — SEQUÊNCIA QUANDO CHEGAR A HORA
================================================================================

1. Instalar Nginx na VPS
2. Configurar Nginx como reverse proxy: porta 443 → localhost:5678
3. Instalar Certbot e gerar certificado SSL (Let's Encrypt) para o domínio
4. Atualizar docker-compose do N8N:
   - N8N_EDITOR_BASE_URL=https://<dominio>
   - WEBHOOK_URL=https://<dominio>
   - Remover N8N_SECURE_COOKIE=false
5. Recriar o container do N8N
6. Atualizar webhook nas instâncias Evolution API para URL HTTPS
7. Fechar porta 5678 no firewall
8. Atualizar redirect URI no Google Cloud Console para HTTPS


================================================================================
HISTÓRICO DE DECISÕES E APRENDIZADOS
================================================================================

09/04/2026 — Domínio e OAuth
  IP não funciona como redirect URI para OAuth (Google e demais provedores rejeitam).
  Solução: usar o domínio srv1546072.hstgr.cloud no lugar do IP.
  N8N_EDITOR_BASE_URL e WEBHOOK_URL devem ser definidas explicitamente no docker-compose.
  Sem isso, o N8N usa o hostname detectado automaticamente (IP).

09/04/2026 — N8N_SECURE_COOKIE=false
  Necessário para acesso via HTTP sem HTTPS.
  Sem isso, o navegador recusa cookies Secure em HTTP e o acesso é bloqueado.
  Apenas para desenvolvimento — remover quando SSL estiver configurado.

09/04/2026 — OAuth Google Calendar
  Configuração que funcionou:
  - redirect URI: http://srv1546072.hstgr.cloud:5678/rest/oauth2-credential/callback
  - Mesma URI registrada no Google Cloud Console
  - Email adicionado como usuário de teste (obrigatório com app em modo de teste)
  - Google Calendar API ativada no projeto do Google Cloud

19/04/2026 — Comunicação entre containers (Evolution API → N8N)
  Containers em redes Docker separadas não se comunicam via 127.0.0.1 nem via domínio.
  A Evolution API (evolution-net) não alcançava o N8N (n8n_default) por nenhum desses endereços.
  Solução: usar o IP do gateway da rede Docker da Evolution API.
  Comando para descobrir o gateway: docker network inspect evolution-net | grep Gateway
  Gateway encontrado: 172.18.0.1
  URL correta do webhook: http://172.18.0.1:5678/webhook/evoapi
  N8N conectado à rede evolution-net via: docker network connect evolution-net n8n
  Configuração persistida no docker-compose — bloco networks com evolution-net como external.

19/04/2026 — docker-compose do N8N com duas redes
  O N8N precisa estar em duas redes simultaneamente: n8n_default (sua própria) e evolution-net (da Evolution API).
  Configuração correta no docker-compose:
    services.n8n.networks: [n8n_default, evolution-net]
    networks.n8n_default.name: n8n_default
    networks.evolution-net.external: true
  Ao recriar o container, se houver erro de label na rede n8n_default:
    docker network rm n8n_default && docker compose up -d

================================================================================
