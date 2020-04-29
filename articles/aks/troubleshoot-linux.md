---
title: Ferramentas de desempenho do Linux
titleSuffix: Azure Kubernetes Service
description: Aprenda a resolver problemas comuns ao utilizar o Serviço Azure Kubernetes (AKS)
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: eb6b126b4d1794adf0380432040190b91a17a675
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77925608"
---
# <a name="linux-performance-troubleshooting"></a>Tiroteio em Problemas de Desempenho linux

A exaustão de recursos nas máquinas Linux é uma questão comum e pode manifestar-se através de uma grande variedade de sintomas. Este documento fornece uma visão geral de alto nível das ferramentas disponíveis para ajudar a diagnosticar tais questões.

Muitas destas ferramentas aceitam um intervalo para produzir a produção de rolamento. Este formato de saída normalmente torna os padrões de spotting muito mais fáceis. Quando aceite, a invocação `[interval]`do exemplo incluirá .

Muitas destas ferramentas têm um vasto histórico e um vasto conjunto de opções de configuração. Esta página fornece apenas um simples subconjunto de invocações para destacar problemas comuns. A fonte canónica de informação é sempre a documentação de referência para cada ferramenta em particular. Essa documentação será muito mais completa do que a fornecida aqui.

## <a name="guidance"></a>Orientação

Seja sistemático na sua abordagem para investigar problemas de desempenho. Duas abordagens comuns são USE (utilização, saturação, erros) e RED (taxa, erros, duração). A RED é normalmente utilizada no contexto dos serviços de monitorização baseada seleção. O USO é normalmente utilizado para monitorizar recursos: para cada recurso numa máquina, monitorização de utilização, saturação e erros. Os quatro principais tipos de recursos em qualquer máquina são cpu, memória, disco e rede. Alta utilização, saturação ou taxas de erro para qualquer um destes recursos indica um possível problema com o sistema. Quando existe um problema, investigue a causa principal: porque é que a latência do disco IO é elevada? Os discos ou a máquina virtual SKU estão estrangulados? Que processos estão a escrever para os dispositivos e para que ficheiros?

Alguns exemplos de questões e indicadores comuns para diagnosticá-los:
- Aceleração do IOPS: utilize iostat para medir os IOPS por dispositivo. Certifique-se de que nenhum disco individual está acima do seu limite, e a soma para todos os discos é inferior ao limite para a máquina virtual.
- Estrangulamento da largura de banda: use iostat como para IOPS, mas medindo a entrada de leitura/escrita. Certifique-se de que tanto por dispositivo como a produção agregada estão abaixo dos limites de largura de banda.
- Exaustão sNAT: isto pode manifestar-se como ligações ativas elevadas (de saída) na Rase. 
- Perda de pacote: isto pode ser medido por procuração através da contagem de retransmissão de TCP relativamente à contagem enviada/recebida. Ambos `sar` `netstat` podem mostrar esta informação.

## <a name="general"></a>Geral

Estas ferramentas são um propósito geral e cobrem informações básicas do sistema. São um bom ponto de partida para uma investigação mais aprofundada.

### <a name="uptime"></a>tempo de uptime

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

uptime fornece sistema uptime e médias de carga de 1, 5 e 15 minutos. Estas médias de carga correspondem aproximadamente a fios que fazem trabalho ou esperam que o trabalho ininterrupto esteja concluído. Em absoluto estes números podem ser difíceis de interpretar, mas medidos ao longo do tempo podem dizer-nos informações úteis:

- Média de 1 minuto > média de 5 minutos significa que a carga está a aumentar.
- Média de 1 minuto < média de 5 minutos significa que a carga está a diminuir.

o tempo de início também pode esclarecer por que razão a informação não está disponível: o problema pode ter sido resolvido por si próprio ou por um reinício antes de o utilizador poder aceder à máquina.

Médias de carga superiores ao número de fios CPU disponíveis podem indicar um problema de desempenho com uma determinada carga de trabalho.

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg despeja o tampão de kernel. Eventos como o OOMKill adicionam uma entrada para o tampão do núcleo. Encontrar um OOMKill ou outras mensagens de exaustão de recursos em registos dmesg é um forte indicador de um problema.

### <a name="top"></a>superior

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top`fornece uma visão geral ampla do estado atual do sistema. Os cabeçalhos fornecem algumas informações agregadas úteis:

- estado das tarefas: correr, dormir, parar.
- Utilização do CPU, neste caso, mostrando principalmente tempo inativo.
- memória total, livre e usada do sistema.

`top`pode perder processos de curta duração; alternativas `htop` `atop` como e fornecem interfaces semelhantes, ao mesmo tempo que fixaalgumas destas deficiências.

## <a name="cpu"></a>CPU

Estas ferramentas fornecem informações de utilização do CPU. Isto é especialmente útil com a saída de rolamento, onde os padrões tornam-se fáceis de detetar.

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat`imprime informações semelhantes do CPU para cima, mas desagregadas por fio CPU. Ver todos os núcleos ao mesmo tempo pode ser útil para detetar uma utilização de CPU altamente desequilibrada, por exemplo, quando uma única aplicação roscada utiliza um núcleo a 100% de utilização. Este problema pode ser mais difícil de detetar quando agregado sobre todos os CPUs do sistema.

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat`fornece informações `mpstat` `top`semelhantes e, enumerando o número de processos à espera da CPU (coluna R), estatísticas de memória e por cento do tempo de CPU gasto em cada estado de trabalho.

## <a name="memory"></a>Memória

A memória é um recurso muito importante, e felizmente fácil de rastrear. Algumas ferramentas podem reportar cpU e memória, como `vmstat`. Mas ferramentas como `free` podem ainda ser úteis para depuração rápida.

### <a name="free"></a>livre

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free`apresenta informações básicas sobre a memória total, bem como a memória usada e livre. `vmstat`pode ser mais útil mesmo para a análise básica da memória devido à sua capacidade de fornecer saída de rolamento.

## <a name="disk"></a>Disco

Estas ferramentas medem iOPS de disco, filas de espera e produção total. 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat`fornece insights profundos sobre a utilização do disco. Esta invocação `-x` passa por `-y` estatísticas estendidas, para saltar as `1 1` médias iniciais do sistema de impressão de saída desde o arranque, e especificar que queremos um intervalo de 1 segundo, terminando após um bloco de saída. 

`iostat`expõe muitas estatísticas úteis:

- `r/s`e `w/s` são leituras por segundo e escreve por segundo. A soma destes valores é IOPS.
- `rkB/s`e `wkB/s` são quilobytes lidos/escritos por segundo. A soma destes valores é a sua entrada.
- `await`é o tempo médio de iowait em milissegundos para pedidos em fila.
- `avgqu-sz`é o tamanho médio da fila sobre o intervalo fornecido.

Num VM Azure:

- a soma `r/s` de `w/s` e para um dispositivo de bloco individual não pode exceder os limites de SKU do disco.
- a soma `rkB/s` de `wkB/s` e para um dispositivo de bloco individual não pode exceder os limites sKU do disco
- a soma `r/s` de `w/s` e para todos os dispositivos de bloco sem pode exceder os limites para o VM SKU.
- a soma `rkB/s` e 'wkB/s para todos os dispositivos de blocos não pode exceder os limites para o VM SKU.

Note que o disco OS conta como um disco gerido do menor SKU correspondente à sua capacidade. Por exemplo, um Disco OS de 1024GB corresponde a um disco P30. Os discos efémeros e os discos temporários não têm limites individuais de disco; são limitados apenas pelos limites vm completos.

Os valores não nulos de espera ou avgqu-sz são também bons indicadores de contenção da OI.

## <a name="network"></a>Rede

Estas ferramentas medem estatísticas de rede como a entrada, falhas de transmissão e utilização. Uma análise mais profunda pode expor estatísticas de TCP de grãos finos sobre congestionamento e pacotes abandonados.

### <a name="sar"></a>sar

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar`é uma ferramenta poderosa para uma ampla gama de análises. Embora este exemplo utilize a sua capacidade de medir estatísticas de rede, é igualmente poderoso para medir cpu e consumo de memória. Este exemplo `sar` invoca `-n` com bandeira `DEV` para especificar a palavra-chave (dispositivo de rede) que exibe a entrada da rede por dispositivo.

- A soma `rxKb/s` de `txKb/s` e é a produção total para um determinado dispositivo. Quando este valor exceder o limite para o Azure NIC provisionado, as cargas de trabalho na máquina sofrerão um aumento da latência da rede.
- `%ifutil`mede a utilização de um determinado dispositivo. À medida que este valor se aproxima dos 100%, as cargas de trabalho sentirão um aumento da latência da rede.

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

Esta invocação `sar` de `TCP,ETCP` utiliza as palavras-chave para examinar as ligações TCP. A terceira coluna da última linha, "retrans", é o número de retransmissões de TCP por segundo. Valores elevados para este campo indicam uma ligação de rede pouco fiável. Na primeira e terceira filas, "ativo" significa uma ligação originária do dispositivo local, enquanto "remota" indica uma ligação de entrada.  Uma questão comum em Azure é `sar` a exaustão da porta SNAT, que pode ajudar a detetar. A exaustão portuário SNAT manifestar-se-ia como valores "ativos" elevados, uma vez que o problema se deve a uma elevada taxa de ligações tCP de saída, iniciadas localmente.

Como `sar` leva um intervalo, imprime a saída de rolamento e, em seguida, imprime as linhas finais de saída contendo os resultados médios da invocação.

### <a name="netstat"></a>netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat`pode introspect uma grande variedade de estatísticas de rede, aqui invocado com saída sumária. Há aqui muitos domínios úteis, dependendo da questão. Um dos domínios úteis na secção TCP são as "tentativas de ligação falhadas". Isto pode ser uma indicação da exaustão da porta SNAT ou de outras questões que fazem ligações de saída. Uma elevada taxa de segmentos retransmitidos (também sob a secção TCP) pode indicar problemas com a entrega de pacotes. 
