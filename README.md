# MCP ABAP ADT Launcher — plugin Eclipse

Plugin Eclipse da **Brain Consulting** para cadastrar ambientes SAP, **on-premise e cloud**, e subir
com um clique o servidor [`mcp-abap-adt`](https://github.com/fr0ster/mcp-abap-adt) de cada um, com
**todas as ferramentas** liberadas e já registrado no **Claude Code**.

> Este repositório contém só a documentação e as instruções de instalação. O plugin é instalado
> pelo update site abaixo.

## Instalação rápida

Update site:

```
https://mcp-abap-adt-eclipse.vercel.app/
```

1. No Eclipse, vá em **Help → Install New Software… → Add…**.
2. Em **Name**, digite `MCP ABAP ADT Launcher`. Em **Location**, cole o endereço acima e clique em **Add**.
3. Marque **Brain Consulting → MCP ABAP ADT Launcher**, clique em **Next** e depois em **Finish**.
4. Aceite o aviso de conteúdo não assinado e reinicie o Eclipse.
5. Abra a view em **Window → Show View → Other… → Brain Consulting → MCP ABAP ADT**.

O passo a passo completo, com requisitos e checklist, está em [INSTALACAO.md](INSTALACAO.md).
As novidades de cada versão estão em [CHANGELOG.md](CHANGELOG.md).

## O que o plugin faz

- Cadastra ambientes SAP (ECC, S/4HANA on-premise, BTP ABAP Environment, S/4HANA Cloud) numa view do Eclipse.
- Inicia e para o servidor `mcp-abap-adt` de cada ambiente, com log no Console do Eclipse.
- Registra cada servidor no Claude Code automaticamente (`~/.claude.json`).
- Botão **>_ Abrir Claude Code**: inicia o servidor do ambiente, espera ele responder e abre o
  Claude Code já conectado, na pasta de trabalho do ambiente.
- No **Windows x64**, já traz o Node.js e o servidor embutidos: não é preciso instalar Node, npm nem o servidor.

## Requisitos

- **Eclipse 2026-09 ou mais novo**, com o ADT instalado (o Java 21 já vem embutido no Eclipse).
- Windows x64: nada além do plugin (Node.js e servidor embutidos).
- Outras plataformas, ou para usar outra versão do servidor: `npm install -g @mcp-abap-adt/core`.
- Claude Code instalado e logado (`claude --version`). Ele não vem com o plugin.

## Formas de conexão

| No cadastro | Para quê | O que o plugin passa ao servidor |
|---|---|---|
| On-premise – usuário e senha (HTTP) | ECC / S/4HANA on-premise | `.env` temporário com `SAP_AUTH_TYPE=basic` |
| On-premise – RFC | Sistemas em que o ADT via HTTP não atende; requer SAP NW RFC SDK | idem + `SAP_CONNECTION_TYPE=rfc` e `--connection-type=rfc` |
| On-premise – certificado X.509 | SSO por certificado de cliente (mTLS), PEM ou PFX | `SAP_AUTH_TYPE=certificate` + caminhos do certificado |
| On-premise – Kerberos/SPNEGO | SSO Windows (requer `npm i -g kerberos` e ticket válido) | `SAP_AUTH_TYPE=kerberos` (+ SPN opcional) |
| BTP / S/4HANA Cloud – service key | ABAP Environment (Steampunk) e similares | `--mcp=<destino>`; o login acontece no navegador |
| Arquivo .env próprio | JWT gerado pelo `mcp-auth`, SNC ou qualquer opção nova do servidor | `--env-path=<arquivo>` |

Para os métodos por URL (usuário/senha, RFC, certificado, Kerberos), o plugin gera um `.env` em
`~/.brain-mcp-abap/run/`, que é a forma documentada pelo servidor. Esse arquivo tem permissão só
para o seu usuário e é apagado quando o servidor para, ou na próxima abertura do Eclipse se ele
tiver sido fechado de forma abrupta.

**Service key (BTP):** no cadastro, use **Importar service key...**. O JSON é copiado para a pasta
de destinos do servidor (Windows: `%USERPROFILE%\Documents\mcp-abap-adt\service-keys\<DESTINO>.json`).
Na primeira subida, o servidor abre o navegador para o login no BTP. Para não precisar logar de
novo a cada reinício, marque **Lembrar o login**, que liga a opção `--unsafe` do servidor e grava os
tokens em disco. Cada ambiente BTP usa a sua própria porta de retorno do login; o padrão começa em 61001.

**Legacy (BASIS < 7.50):** o projeto `mcp-abap-adt` marca esse suporte como suspenso no momento.
Para ECC antigo, cadastre como on-premise. As ferramentas que dependem de serviços ADT
inexistentes no sistema vão falhar com erro do SAP.

## Ferramentas

Na aba **Ferramentas** do cadastro:

- **Leitura (readonly):** fonte, where-used, metadados, estrutura, pacotes, transportes, conteúdo
  de tabelas, SQL, dumps, traces, AST e análise semântica.
- **Alto nível (high):** criar, alterar, ativar e excluir classes, programas, DDIC, CDS, RAP,
  transportes, ABAP Unit e ATC.
- **Baixo nível (low):** operações ADT passo a passo (lock/unlock, check, validate, updates granulares).

`high` e `low` são mutuamente exclusivos: o servidor não sobe com os dois. O padrão é
`--exposition=readonly,high`, que cobre leitura e todo o ciclo de criar, alterar e ativar objetos.
Busca e informações de sistema vêm sempre. O **Tipo de sistema** (on-premise ou cloud) filtra o que
não existe no ambiente; por exemplo, programas não aparecem em ABAP Cloud.

A fachada "compact" (22 ferramentas genéricas) é outro comando, `mcp-abap-adt-compact`. Para usá-la,
aponte o executável para ele em **Configurações**.

## Usar

1. **Configurações…** (botão direito na tabela, ou menu ⋮ da view): confira se **Servidor MCP**
   está como **Embutido no plugin**.
2. **+ Adicionar**: escolha a forma de conexão e preencha os campos que aparecem.
3. **Testar conexão**: o diagnóstico depende do método (login, certificado, Negotiate, service key, `.env`).
4. **▶ Iniciar**: o Console mostra conexão, tipo de sistema, ferramentas e o log do servidor.
5. Clique em **>_ Abrir Claude Code**, ou abra uma nova sessão do Claude Code e use `/mcp`.

## Atualizar

Com o site cadastrado, use **Help → Check for Updates**. Os ambientes e as senhas são mantidos.

Se o Eclipse disser que não há atualizações logo depois de uma publicação, vá em
**Window → Preferences → Install/Update → Available Software Sites**, selecione o site e clique em **Reload**.

## Onde ficam os dados

| O quê | Onde |
|---|---|
| Ambientes e configurações (sem senhas) | `~/.brain-mcp-abap/environments.json` |
| Senhas e senha do .pfx | Secure Storage do Eclipse (criptografado) |
| `.env` temporário dos servidores em execução | `~/.brain-mcp-abap/run/` (só o dono; apagado ao parar) |
| Service keys | pasta de destinos do mcp-abap-adt (configurável) |
| Registro no Claude Code | `~/.claude.json` → `mcpServers` (backup `.brain-mcp.bak`) |

## Avançado

Na aba **Avançado** de cada ambiente:

- **Argumentos extras**, por exemplo `--auth-broker-path=...` ou `--browser=none`.
- **Variáveis extras** no formato `CHAVE=valor`, por exemplo `AUTH_LOG_LEVEL=debug` ou
  `DEBUG_CONNECTORS=true`. Elas prevalecem sobre as geradas pelo plugin.

Assim você usa opções novas do servidor, que evolui rápido, sem esperar uma versão do plugin.

## Solução de problemas

| Sintoma | Causa provável |
|---|---|
| Teste: "Tempo esgotado ao conectar" | VPN, porta ou firewall. Confira a porta na SMICM. |
| Teste: "Falha TLS/SSL" | `https` numa porta HTTP (ou o contrário), ou certificado interno. |
| Kerberos: "oferece NTLM" | O ICM não oferece SPNEGO para o usuário; peça ao Basis. |
| Certificado: HTTP 401/403 | Certificado não mapeado (CERTRULE) ou o ICM não pede certificado de cliente. |
| Service key: login não abre | Porta de retorno ocupada, ou `--browser=none` nos extras. |
| Processo "terminou sozinho" | Leia o Console: argumento não aceito pela versão instalada, credencial etc. |
| Claude não vê o servidor | Nova sessão do Claude Code ou `/mcp`; confira o endpoint em Configurações. |

Mais casos de instalação estão em [INSTALACAO.md](INSTALACAO.md#6-problemas-comuns-na-instalação).

## Licenças de terceiros

O plugin embute, sem modificações, o servidor [`@mcp-abap-adt/core`](https://github.com/fr0ster/mcp-abap-adt),
distribuído sob **AGPL-3.0**, e dependências sob LGPL-3.0, além do Node.js. O servidor roda como
processo separado. A lista de licenças e o endereço do código-fonte correspondente estão no arquivo
`runtime/THIRD-PARTY.txt`, incluído no plugin.

O Claude Code não é distribuído com o plugin; ele deve ser instalado à parte, conforme os termos da Anthropic.

---

Brain Consulting — *Por pessoas, para pessoas, gerando inovação* — [brainconsulting.info](https://brainconsulting.info)
