---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: e5a6dae98e786bf55dc17d8fabe42f84e9927442
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91606002"
---
![Documentação Dsv3](media/vm-disk-performance/dsv3-documentation.jpg)

A produção máxima de disco **não acoched** é o limite máximo de armazenamento predefinido que a máquina virtual é capaz de manusear. O limite máximo de produção **em cache** é um limite separado quando ativa o cache do anfitrião. O caching do anfitrião funciona aproximando o armazenamento do VM que pode ser escrito ou lido rapidamente. A quantidade de armazenamento disponível para o VM para o caching hospedeiro está na documentação. Por exemplo, pode ver o Standard_D8s_v3 vem com 200 GiB de armazenamento de cache.

Ativar o cache do anfitrião pode ser feito ao criar a sua máquina virtual e a anexar discos. Também pode ajustar-se para ligar e desligar o hospedeiro, cachendo os seus discos num VM existente.

![Caching hospedeiro](media/vm-disk-performance/host-caching.jpg)

O cache do anfitrião pode ser ajustado de modo a corresponder aos seus requisitos de carga de trabalho para cada disco. Pode definir o seu caching do anfitrião para ser apenas para leitura apenas para cargas de trabalho que apenas fazem operações de leitura e Ler/escrever para cargas de trabalho que fazem um balanço de operações de leitura e escrita. Se a sua carga de trabalho não seguir nenhum desses padrões, não recomendamos a utilização de caching hospedeiro. 

Vamos analisar alguns exemplos de diferentes configurações de cache do anfitrião e ver como isso afeta o fluxo de dados e o desempenho. Neste primeiro exemplo, vamos analisar o que acontece com os pedidos de IO quando a definição de caching do anfitrião está definida **apenas**para Ler .

Configurar:
- Standard_D8s_v3 
    - IOPS em cache: 16.000
    - IOPS sem cocó: 12.800
- Disco de dados P30 
    - IOPS: 5.000
    - **Caching do anfitrião: Só para leitura** 

Quando uma leitura é realizada e os dados desejados estão disponíveis na cache, a cache devolve os dados solicitados e não há necessidade de ler a partir do disco. Esta leitura é contada para os limites em cache do VM.

![Leia o hospedeiro Caching Read Hit](media/vm-disk-performance/host-caching-read-hit.jpg)

Quando uma leitura é realizada e os dados desejados **não** estão disponíveis na cache, o pedido de leitura é então retransmitido para o disco que, em seguida, o surface para a cache e o VM. Esta leitura é contada tanto para o limite não encatado do VM como para o limite de cache do VM.

![Leia o anfitrião Caching Read miss](media/vm-disk-performance/host-caching-read-miss.jpg)

Quando uma escrita é executada, a escrita tem de ser escrita tanto para a cache como para o disco antes de ser considerada completa. Esta escrita é contada para o limite não encatado do VM e o limite de cache do VM.

![Leia o anfitrião Caching Write](media/vm-disk-performance/host-caching-write.jpg)

Neste próximo exemplo, vamos ver o que acontece com os pedidos de IO quando a definição de cache do anfitrião está definida para **Ler/escrever**.

Configurar:
- Standard_D8s_v3 
    - IOPS em cache: 16.000
    - IOPS sem cocó: 12.800
- Disco de dados P30 
    - IOPS: 5.000
    - **Caching do anfitrião: Ler/escrever** 

As leituras são tratadas da mesma forma que read-only, as escritas são a única coisa diferente com o caching de Leitura/escrita. Ao escrever com o conjunto de cache do anfitrião para Ler/escrever, a escrita só precisa de ser escrita para a cache do anfitrião para ser considerada completa. A escrita é então preguiçosamente escrita para o disco como um processo de fundo. Isto significa que as escritas serão contadas para IO em cache quando estiver escrita para a cache e quando estiver preguiçosamente escrita para o disco contará para o IO não encatado.

![Ler/escrever Caching Do anfitrião](media/vm-disk-performance/host-caching-read-write.jpg)

Vamos continuar com um exemplo com a nossa Standard_D8s_v3 máquina virtual. Só que desta vez, vamos permitir o cache do anfitrião nos discos e agora o limite de IOPS do VM é de 16.000 IOPS. Anexados ao VM estão três discos P30 subjacentes que aguentam 5.000 IOPS.

Configurar:
- Standard_D8s_v3 
    - IOPS em cache: 16.000
    - IOPS sem cocó: 12.800
- Disco P30 OS 
    - IOPS: 5.000
    - Caching do anfitrião: Ler/escrever 
- 2 Discos de Dados P30
    - IOPS: 5.000
    - Caching do anfitrião: Ler/escrever

![Exemplo de Caching do anfitrião](media/vm-disk-performance/host-caching-example-without-remote.jpg)

Agora, a aplicação que utiliza esta Standard_D8s_v3 máquina virtual com caching ativada faz um pedido de 15.000 IOPS. Esses pedidos são divididos como 5.000 IOPS a cada disco subjacente ligado e não ocorre nenhuma cobertura de desempenho.

## <a name="combined-uncached-and-cached-limits"></a>Limites combinados sem cocó e em cache

Os limites de caching de uma máquina virtual são separados dos seus limites não tapados. Isto significa que pode ativar o cache do anfitrião em discos ligados a um vm, ao mesmo tempo que não permite que o hospedeiro caching em outros discos permita que as suas máquinas virtuais obtenham uma IO de armazenamento total do limite de cache mais o limite não colado. Vamos analisar um exemplo disso para ajudar a solidificar a forma como estes limites funcionam em conjunto e vamos continuar com a Standard_D8s_v3 configuração virtual de máquinas virtuais e discos premium anexados.

Configurar:
- Standard_D8s_v3 
    - IOPS em cache: 16.000
    - IOPS sem cocó: 12.800
- Disco P30 OS 
    - IOPS: 5.000
    - Caching do anfitrião: Ler/escrever
- 2 Discos de dados P30 X 2
    - IOPS: 5.000
    - Caching do anfitrião: Ler/escrever
- 2 Discos de dados P30 X 2
    - IOPS: 5.000
    - Caching hospedeiro: Deficiente

![Exemplo de caching do anfitrião com armazenamento remoto](media/vm-disk-performance/host-caching-example-with-remote.jpg)

Agora, a aplicação em execução na Standard_D8s_v3 máquina virtual com um pedido de 25.000 IOPS. Este pedido é dividido como 5.000 IOPS a cada disco subjacente ligado onde 3 desses discos estão a usar cache hospedeiro e 2 dos discos não. Uma vez que os 3 caching hospedeiros estão dentro dos limites em cache de 16.000, esses pedidos são concluídos com sucesso e não ocorre nenhuma cobertura de desempenho de armazenamento. Além disso, uma vez que os 2 discos que não utilizam o cache do hospedeiro estão dentro dos limites não cobertos de 12.800, esses pedidos também são concluídos com sucesso e não ocorre nenhuma cobertura.

## <a name="metrics-for-disk-performance"></a>Métricas para desempenho do disco
Temos métricas no Azure que fornecem uma visão de como as suas máquinas virtuais e discos estão a funcionar. Estas métricas podem ser vistas visualmente através do portal Azure ou podem ser recuperadas através de uma chamada de API. As métricas são calculadas em intervalos de um minuto. As seguintes Métricas estão disponíveis para obter informações sobre VM e Disk IO e desempenho de produção:
- **Profundidade da fila** do disco os - O número de IO pendentes atuais solicita que esperem ser lidos ou escritos para o disco OS.
- **Leitura de discos oss/seg** – O número de Bytes que são lidos num segundo a partir do disco OS.
- **Operações de leitura do disco oss/sec** – O número de operações de entrada que são lidas num segundo a partir do disco OS.
- **OS Disk Write Bytes/Sec** – O número de Bytes que são escritos num segundo a partir do disco OS.
- **Operações de escrita do disco oss/sec** – O número de operações de saída que são escritas num segundo a partir do disco OS.
- **Data Disk Queue Depth** – O número de IO em curso atuais solicita que esperem ser lidos ou escritos para o(s) disco de dados.
- **Ficha de Dados Ler Bytes/Sec** – O número de Bytes que são lidos num segundo a partir do ou dos discos de dados.
- **Operações de leitura de discos de dados/Sec** – O número de operações de entrada que são lidas num segundo a partir de um ou de um disco de dados.
- **Data Disk Write Bytes/Sec** – O número de Bytes que são escritos num segundo a partir do ou dos discos de dados.
- Operações de escrita de discos de **dados/Sec** – O número de operações de saída que são escritas num segundo a partir de um ou de um disco de dados.
- **Leitura de Discos/Sec** – O número de bytes totais que são lidos num segundo de todos os discos ligados a um VM.
- Operações de leitura de **disco/Sec** – O número de operações de entrada que são lidas num segundo de todos os discos ligados a um VM.
- **Disk Write Bytes/Sec** – O número de Bytes que são escritos num segundo de todos os discos ligados a um VM.
- Operações de escrita de **disco/Sec** – O número de operações de saída que são escritas num segundo de todos os discos ligados a um VM.

## <a name="storage-io-utilization-metrics"></a>Métricas de utilização de IO de armazenamento
Métricas que ajudam a diagnosticar a tampa do IO do disco:
- **Percentagem consumida** pelo disco de dados - a percentagem calculada pelo disco de dados IOPS concluída sobre o disco de dados IOPS. Se este valor estiver a 100%, a sua aplicação em execução será limitada a partir do limite de IOPS do seu disco de dados.
- **Percentagem consumida** pela largura de banda do disco de dados - a percentagem calculada pela produção do disco de dados concluída sobre o rendimento do disco de dados fortado. Se este valor estiver a 100%, a sua aplicação em execução será limitada a partir do limite de largura de banda do seu disco de dados.
- **Percentagem consumida** pelo disco oss - a percentagem calculada pelo disco os IOPS concluída sobre o disco de oss IOPS. Se este valor for de 100%, a sua aplicação em execução será limitada ao limite de IOPS do seu disco de SO.
- **Os Discos de Banda consumida Percentagem Consumida** - a percentagem calculada pela produção do disco os concluída sobre a produção do disco de OS fortada. Se este valor estiver a 100%, a sua aplicação em execução será limitada ao limite de largura de banda do seu disco de SO.

Métricas que ajudam a diagnosticar a tampa de IO VM:
- **VM Cached IOPS Percentagem consumida** - a percentagem calculada pelo total de IOPS concluída acima do limite máximo da máquina virtual em cache IOPS. Se este valor estiver a 100%, a sua aplicação em execução será limitada ao limite de IOPS em cache do seu VM.
- **VM Cached Bandwidth Consumed Percentagem** - a percentagem calculada pela produção total do disco concluída sobre a potência da máquina virtual em cache máximo. Se este valor estiver a 100%, a sua aplicação em execução será limitada ao limite de largura de banda em cache do seu VM.
- **VM IOPS Educar A Percentagem Consumida** - a percentagem calculada pelo total de IOPS numa máquina virtual concluída sobre o limite máximo de IOPS da máquina virtual não-acolhida. Se este valor estiver a 100%, a sua aplicação em execução será limitada a partir do limite IOPS não coberto do seu VM.
- **VM Percentagem consumida** pela largura de banda não colada - a percentagem calculada pela produção total do disco numa máquina virtual concluída sobre a potência máxima da máquina virtual. Se este valor estiver a 100%, a sua aplicação em execução será limitada a partir do limite de largura de banda não coberta do seu VM.

