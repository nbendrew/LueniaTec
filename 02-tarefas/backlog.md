# Backlog
Última atualização: 09/04/2026

---

## LLM / Agente

- [ ] Testar comportamento em situações de borda:
  - Pedido ambíguo ou incompleto ("uma pizza" sem sabor)
  - Múltiplos itens na mesma mensagem
  - Mudança de pedido no meio da conversa
  - Endereço incompleto
  - Item fora do cardápio (prevenção de alucinação)

---

## N8N

- [ ] Tratamento de áudios — transcrição antes de passar para a LLM
- [ ] Tratamento de erros no fluxo (o que fazer se a LLM não responder, se o Supabase falhar, etc.)

---

## Supabase

- [ ] Definir estrutura definitiva da tabela `pedidos` para registro de pedidos finalizados
- [ ] Planejar como o agente vai registrar um pedido finalizado (trigger ou nó específico no N8N)

---

## Infraestrutura

- [ ] Configurar firewall — revisar portas expostas desnecessariamente
- [ ] Planejar SSL/HTTPS com domínio próprio
- [ ] Configurar monitoramento básico dos containers (uptime, restart automático)

---

## Negócio / Produto

- [ ] Formalizar abertura do ME no Simples Nacional (CNAE 6201-5/01)
- [ ] Contratar contador — escritório digital, R$100–150/mês
- [ ] Definir precificação do MVP
- [ ] Identificar e abordar primeiros clientes para teste
- [ ] Planejar escopo e interface do painel web (gerenciamento de cardápio, promoções e pedidos)
- [ ] Planejar ativação/desativação de promoções via comando no WhatsApp (MVP)

## Skills — Luenia Foods

- [x] Skill de criativos unificada (`criativos.skill`) — cobre posts únicos e carrosséis, 5 estilos, 2 perfis de marca
- [ ] Construir skill de personal branding
- [ ] Construir skill de prova social e cases (aguarda clientes reais)

---

## Automação de Conteúdo — Instagram

Pipeline N8N para produção e postagem automática de carrosséis.
Fluxo: Schedule Trigger → Claude API → Ideogram API → Canva API → Telegram (aprovação) → Buffer (postagem)

- [ ] Decidir entre versão completa (~R$150/mês) ou econômica (~R$55/mês)
  - Completa: N8N Cloud + Claude API + Ideogram + Buffer
  - Econômica: N8N self-hosted + Flux + Meta Business Suite
- [ ] Gerar JSON do workflow completo para importar no N8N
- [ ] Configurar nó da Claude API — geração de tema + roteiro + prompts
- [ ] Configurar nó da Ideogram API — geração de imagens
- [ ] Testar integração com Canva API — montagem automática dos slides
- [ ] Configurar Telegram para envio de preview para aprovação
- [ ] Configurar Buffer para agendamento e postagem no Instagram
- [ ] Testar fluxo completo de ponta a ponta

