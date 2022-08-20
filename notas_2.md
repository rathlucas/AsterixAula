# Dia 2

## Dados conexão com o tronco

- IP Proxy:
- Usuário: 5511523200XX (XX -> Final do seu IP)
- Senha:

## Realizar a configuração

- Abrir o arquivo sip.conf
- `[operadora]`
  - host=`Ip do Proxy`
  - fromuser=`Usuário`
  - username=`Mesmo Usuário`
  - type=peer
  - context=entrada
  - disallow=all
  - allow=alaw,ulaw,gsm
  - insecure=port,invite

## Register no sip.conf

- Procurar o bloco de comandos register e adicionar:
  - register => 551152320012:123123@192.168.2.9
- Abrir a CLI do Asterisk
  - `sip reload` para atualizar as mudanças realizadas no sip.conf
  - `sip show registry` para verificar a conexão

## Configurando context no extensions.conf

- Definir context `[entrada]`
  - `exten => 551152320012,1,Dial(SIP/2000)` -> Definir o número de discagem
- Nos `[ramais]` definir a saída:
  - `exten => _0ZZ[2-9]X.,1,Dial(SIP/operadora/55${EXTEN:1})` -> Este comando corta o 55 do número, dá match no DDD através de uma expressão regular e faz a ligação.
  - `exten => 29,1,Record(nossaura:gsm)` -> discar 29 grava o audio e salva em nossaura, podendo ser reutilizado depois. Parar a gravação com #
- dialplan reload
- Realizar a ligação para testes

## Desenvolvendo URA

- No context `[entrada]`
  - `exten => 551152320012,1,NoOp("Entrada da nossa URA")`
  - `same => n,Answer()` -> atender
  - `same => n,Wait(1)` -> é uma boa prática usar o Answer e esperar com Wait, para garantir que a URA não será cortada
  - `same => n,Read(OPCAO,nossaura,1)` -> Ler a opção selecionada e tocar o audio gravado em nossaura
  - `same => n,GoToIf($["${OPCAO}" = ""]?timeout,1)` -> se uma opção não for selecionada, transferir para timeout
  - `same => n,GoTo(${OPCAO},1)` -> lógica de transferência para as opções da ura
  - `exten => 1,1,Playback(tt-monkeys)`
  - `exten => 2,1,Playback(SIP/2000)`
  - `exten => 3,1,Hangup()` -> Desligar
- `core set verbose 10` -> para ativar o modo verboso e facilitar o debugging

## Criando uma estratégia / configurando a fila

- Acessar `queues.conf`
- Definir context `[suporte]`:
  - `strategy=ringall`
  - `maxlen=` -> tamanho máximo da fila
  - `timeout=`
  - `retry=`
  - `weight=` -> prioridade
  - `wrapuptime=` -> tempo de pausa produtiva, padrão 0
  - `member => SIP/2000,0,Fulano` -> primeiro ramal
  - `member => SIP/2001,0,Beltrano` -> segundo ramal
- Acessar a CLI do Asterisk e executar o comando `module reload app_queue.so` para persistir as mudanças
- Para mostrar as filas, membros e status `queue show`

### Integrando no extensions.conf

- `same => n,MixMonitor(src-${CALLERID(num)}-dst-${EXTEN}-${UNIQUEID}.WAV)` -> enviado após o Answer
- `exten => 4,1,Queue(suporte)` -> adicionado como opção de discagem na URA
- audio gravado fica salvo em /var/spool/asterisk/monitor
- asterisk salva cdr em /var/log/asterisk/cdr-csv

## Conceitos avançados em produção

- Limpar os arquivos utilizados, deixar apeenas o essencial
- `includes` e `#include` para importar arquivos
