# Asterisk e WebRTC

## Login SSH

- ip: 192.168.2.12
- user: root
- password: mvsp123@

## Asterisk

Framework de comunicação com integração chat | vídeo | voz.

### Pré-requisitos

- Sistemas base linux.

## Configuração

- Desabilitar selinux
- Realizar o download da versão 16.28.0 LTS do Asterisk
- Extrair o tar utilizando `tar xzfv nome_do_arquivo`
- ./configure --with-jansson-bundled --with-pjproject-bundled
- Build: make && make install
- Depois da build e instalação: make config e make samples
- Startar o serviço do Asterisk: service start asterisk

## Iniciando a CLI

- asterisk -r ou rasterisk

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

- extensions.conf
- exten => 2424,1,NoOp("descrição") // log
- exten => 2424,2,Playback(tt-monkeys) // toca audio de macaco
- dialplan reload na CLI asterisk para efetivar as mudanças

### Boas práticas

- same => n,NoOp("utilizar o same para agrupar hosts baseado na prioridade 1") // n(pode ser definido um nome para referência futura)

## Conectando ramal

- exten => \_1XXX,1,Dial(sip/${EXTEN})
