================================================================================
CONTEXTO DO CHAT — INFRAESTRUTURA / VPS
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
OS:         Ubuntu 24.04 LTS
Acesso:     ssh root@187.127.7.78


================================================================================
CONTAINERS DOCKER EM EXECUÇÃO
================================================================================

Todos os containers ficam em /opt/evolution/

| serviço         | imagem                              | porta | pasta              |
|-----------------|-------------------------------------|-------|--------------------|
| PostgreSQL 15   | postgres:15                         | 5432  | /opt/evolution/postgres/ |
| Redis Alpine    | redis:alpine                        | 6379  | /opt/evolution/redis/    |
| Evolution API   | evoapicloud/evolution-api:v2.3.7    | 8080  | /opt/evolution/api/      |

Container planejado:
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

/opt/n8n/                    ← ainda não criado
└── docker-compose.yaml


================================================================================
CREDENCIAIS E ACESSOS
================================================================================

Evolution API Key:  tolokodedroga
Evolution URL:      http://187.127.7.78:8080
Evolution Manager:  http://187.127.7.78:8080/manager
N8N URL (futuro):   http://187.127.7.78:5678


================================================================================
PENDÊNCIAS / PRÓXIMOS PASSOS NESTE TEMA
================================================================================

- Instalar N8N via Docker em /opt/n8n/
- Avaliar configuração de firewall (portas expostas)
- Planejar SSL/HTTPS com domínio próprio (futuro)
- Configurar monitoramento básico dos containers


================================================================================
HISTÓRICO DE DECISÕES E APRENDIZADOS
================================================================================

No bloco de containers, adicionar a linha do N8N que antes estava como "planejado":
| N8N  | n8nio/n8n:latest  | 5678  | /opt/n8n/  |
No bloco de estrutura de arquivos, atualizar:
/opt/n8n/
└── docker-compose.yaml   ← criado e funcionando


================================================================================
