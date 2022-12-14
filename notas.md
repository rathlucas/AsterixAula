# Asterisk e WebRTC

## Login SSH

- ip: SeuIp
- user: SeuNomeDeUsuario
- password: SuaSenha

## Asterisk

Framework de comunicação com integração chat | vídeo | voz.

### Pré-requisitos

- Sistemas base linux.

## Configuração

- Desabilitar selinux
- Realizar o download da versão `16.28.0 LTS` do Asterisk
- Extrair o tar utilizando `tar xzfv nome_do_arquivo`
- Entrar no diretório utilizando o comando `cd nome_do_diretório`
- ./configure --with-jansson-bundled --with-pjproject-bundled
  - Se o seguinte comando retornar algum erro, talvez seja necessário instalar algumas depêndencias de build. As depêndencias podem variar entre sistemas, porém a CLI provém informações detalhadas sobre como prosseguir
- Para realizar o processo de build: `make && make install`
- Depois da build e instalação: `make config` e `make samples`
- Startar o serviço do Asterisk: `service start asterisk`

## Iniciando a CLI

- `asterisk -r` ou `rasterisk`

## Configurando sip

- /etc/asterisk e abrir sip.conf
- Criar contexto do ramal no final do arquivo \[1000] sintaxe
- type=(user, peer ou friend) // user envia, peer recebe e friend realiza os dois
- defaultuser=1000 e fromuser=1000 // dados do user
- secret='senha' // senha
- host=dynamic // tipo do host
- context=ramais
- disallow=all e allow=alaw,ulaw,gsm // disallow all para bloquear todos os codecs, depois allow nos que serão utilizados
- call-limit=2 // limita o número de conexões na chamada
- sip reload no CLI do asterisk para efetivar as mudanças

## CLI Asterisk

- sip show peers e sip show users
- Conectar linphone pelo sip

## Configurando extensions

- Acessar o arquivo extensions.conf
- Criar um novo contexto `[ramais]`
- `exten => 2424,1,NoOp("descrição")` // log
- `exten => 2424,2,Playback(tt-monkeys)` // toca audio de macaco
- `dialplan reload` na CLI asterisk para efetivar as mudanças

### Boas práticas

- `same => n,NoOp("utilizar o same para agrupar hosts baseado na prioridade 1")` // n(pode ser definido um nome para referência futura)

## Conectando ramal

- exten => \_1XXX,1,Dial(sip/${EXTEN})
