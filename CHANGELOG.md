# Novidades por versão

## 1.3.1

- O botão **>_ Abrir Claude Code** encontra o `claude` mesmo quando o Eclipse foi aberto antes da
  instalação do Claude Code. O plugin procura no PATH do Eclipse, no PATH atual do Windows (lido do
  registro) e nas pastas padrão (`%USERPROFILE%\.local\bin`, `%APPDATA%\npm`). O terminal aberto
  também recebe o PATH atualizado.
- Novo campo opcional **Executável do claude** em Configurações, para quando o Claude Code está
  numa pasta fora do padrão.

## 1.3

- **Botão ">_ Abrir Claude Code"** na view: com um ambiente selecionado, inicia o servidor dele (se
  ainda não estiver rodando), espera o servidor responder e abre o Claude Code já com o `/mcp`
  conectado. Sem ambiente selecionado, abre na pasta do usuário.
- **Onde abrir** (Configurações): janela do **Windows Terminal** (padrão; sem ele, usa o console do
  Windows) ou a **view Terminal do Eclipse**. Se o Terminal do Eclipse não estiver disponível, o
  plugin usa a janela externa automaticamente.
- **Pasta de trabalho por ambiente** (aba **Claude Code** do cadastro): por exemplo, a pasta do
  projeto do cliente, para o Claude ler as especificações e o `CLAUDE.md` de lá.
- **Argumentos do claude** (Configurações), por exemplo `--continue` para retomar a última conversa.
- Requer o Claude Code instalado (`claude --version`). Ele não é embutido no plugin.

## 1.2

- **Servidor embutido:** no Windows x64, o plugin já traz o Node.js e o `mcp-abap-adt`. Não é
  preciso instalar Node, npm nem o servidor. As versões aparecem em **Configurações** e no Console
  ao iniciar.
- A opção "Executável instalado na máquina" continua disponível em **Configurações**, para usar
  outra versão do servidor ou outra plataforma.

## 1.1

- **Grupos de ferramentas por ambiente:** `readonly` + `high` (padrão) ou `readonly` + `low`.
  O servidor recusa `high` e `low` juntos.
- **Todas as formas de conexão do servidor:** usuário/senha HTTP, RFC, certificado X.509 (PEM ou
  PFX), Kerberos/SPNEGO, service key do BTP (login no navegador) e `.env` próprio.
- **Tipo de sistema** (`SAP_SYSTEM_TYPE` = onprem/cloud), que decide quais ferramentas o servidor oferece.
- **Teste de conexão específico** para cada forma de conexão.
- **Padrões corrigidos para o servidor:** endpoint `/mcp/stream/http` e `--port=N`. Configurações
  salvas pela 1.0 são migradas sozinhas.
