# Sessão — 19/04/2026
**Tema:** Infraestrutura — Resolução de comunicação entre containers
**Participantes:** Sócios

---

## O que foi feito

### Problema identificado
A Evolution API não conseguia alcançar o N8N após mudanças no docker-compose.
Erro nos logs: `connect ECONNREFUSED 127.0.1.1:5678`

A Evolution API resolvia o domínio `srv1546072.hstgr.cloud` para `127.0.1.1` (hostname local da VPS),
que é diferente de `127.0.0.1`. Containers em redes Docker separadas são isolados — não se comunicam
via loopback nem via domínio externo.

### Diagnóstico
- N8N estava na rede `n8n_default`
- Evolution API estava na rede `evolution-net`
- Redes Docker diferentes = isolamento de rede = ECONNREFUSED

### Solução aplicada
1. Descobriu-se o IP do gateway da rede `evolution-net`:
   ```
   docker network inspect evolution-net | grep Gateway
   → 172.18.0.1
   ```
2. Webhook da wa4 atualizado para `http://172.18.0.1:5678/webhook/evoapi`
3. N8N conectado à rede `evolution-net`: `docker network connect evolution-net n8n`

### Persistência da configuração
docker-compose.yaml do N8N atualizado com bloco networks:
```yaml
networks:
  n8n_default:
    name: n8n_default
  evolution-net:
    external: true
```
E serviço n8n declarado com as duas redes:
```yaml
networks:
  - n8n_default
  - evolution-net
```

### Resultado
Sofia respondendo normalmente via WhatsApp. Sistema funcionando de ponta a ponta.

---

## Problemas resolvidos

- ECONNREFUSED ao enviar webhook da Evolution API para o N8N
- Conflito de label na rede n8n_default ao recriar container:
  `docker network rm n8n_default && docker compose up -d`

---

## Aprendizados

- Containers em redes Docker separadas não se comunicam via 127.0.0.1 nem domínio
- Usar IP do gateway da rede Docker para comunicação interna entre stacks diferentes
- `docker network connect` resolve no momento mas não persiste — sempre atualizar o docker-compose

---

## Próximos passos

1. Testar fluxo completo de pedido — do "oi" até a finalização
2. Corrigir `=` no nó extrairResposta
3. Corrigir bug pizza meia a meia no system prompt
4. Adicionar coluna `endereco` em `estabelecimentos`
