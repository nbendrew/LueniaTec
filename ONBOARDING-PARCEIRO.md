# 🚀 Onboarding — Acesso ao Vault do Projeto

Guia completo para configurar o mesmo ambiente de trabalho com Obsidian + Claude (IA) integrados.

---

## 📋 O que você vai ter no final

- Obsidian apontando para o repositório do projeto (GitHub)
- Claude (IA) conectado ao vault via plugin MCP
- Acesso completo às notas, contextos e tarefas do projeto
- O agente gerente de projeto funcionando igual ao do seu parceiro

---

## PASSO 1 — Instalar o Git (se não tiver)

1. Acesse **https://git-scm.com** e baixe o Git para seu sistema operacional
2. Instale com as opções padrão

> Se já usa Git, pule para o Passo 2.

---

## PASSO 2 — Clonar o repositório do vault

Abra o terminal (ou Git Bash no Windows) e rode:

```bash
git clone https://github.com/SEU_USUARIO/SEU_REPOSITORIO.git
```

> ⚠️ Substitua pela URL real do repositório — peça ao seu parceiro se não souber.

Isso vai criar uma pasta local com todos os arquivos do projeto já organizados.

---

## PASSO 3 — Instalar o Obsidian e abrir o vault

1. Acesse **https://obsidian.md** e instale o Obsidian
2. Abra o Obsidian → clique em **"Open folder as vault"**
3. Selecione a pasta que foi criada pelo `git clone` no passo anterior
4. O Obsidian vai carregar todo o vault com a estrutura do projeto pronta ✅

---

## PASSO 4 — Instalar o plugin Local REST API no Obsidian

Este plugin é o que permite o Claude acessar e editar o vault.

1. No Obsidian, vá em **Configurações** (ícone de engrenagem) → **Community plugins**
2. Clique em **"Turn on community plugins"** (se ainda não estiver ativo)
3. Clique em **"Browse"** e pesquise por **`Local REST API`**
4. Instale e **ative** o plugin
5. Vá nas configurações do plugin e **copie a API Key** gerada — você vai precisar dela no próximo passo

---

## PASSO 5 — Conectar o Claude ao Obsidian

1. Acesse **https://claude.ai** e faça login
2. Vá em **Configurações** → **Connections** (ou "Integrações")
3. Procure por **Obsidian** na lista de conectores disponíveis
4. Clique em **Connect** e insira:
   - **API Key** copiada no passo anterior
   - **URL** do servidor local (geralmente `https://127.0.0.1:27124`)
5. Autorize a conexão

> ⚠️ **Importante:** O Obsidian precisa estar **aberto** no computador sempre que quiser usar o Claude com o vault.

---

## PASSO 6 — Testar

Com tudo configurado, abra o Claude e envie a mensagem:

> *"Liste todos os arquivos do meu vault do Obsidian"*

Se aparecer a lista de arquivos das pastas `00-contexto`, `02-tarefas` etc., está tudo funcionando! ✅

---

## 🔄 Mantendo o vault sincronizado

Como o vault está no GitHub, para puxar as atualizações do seu parceiro basta rodar na pasta do vault:

```bash
git pull
```

E para enviar suas alterações:

```bash
git add .
git commit -m "descrição do que foi alterado"
git push
```

---

## 🤖 Como usar o agente do projeto

Com o Claude conectado, você pode usar os seguintes comandos na conversa:

| Comando | O que faz |
|---|---|
| *(sem comando)* | Gera um resumo do estado atual do projeto |
| `/resumo` | Resumo executivo completo |
| `/tarefas` | Lista tarefas por status |
| `/sessao [descrição]` | Registra uma sessão de trabalho |
| `/decisao [título]` | Registra uma decisão técnica |
| `/problema [título]` | Registra um problema resolvido |
| `/atualizar-contexto [arquivo]` | Atualiza um contexto específico |

---

## ❓ Problemas comuns

**Claude não encontra os arquivos:**
→ Verifique se o Obsidian está aberto e o plugin Local REST API está ativo

**Erro de conexão:**
→ Confirme que a URL é `https://127.0.0.1:27124` e que a API Key está correta

**Plugin não aparece na busca:**
→ Certifique-se de que os "Community plugins" estão habilitados nas configurações

**Vault abre vazio:**
→ Confirme que selecionou a pasta correta no "Open folder as vault" — deve ser a pasta clonada do GitHub

---

*Criado em: 2026-04-05*
