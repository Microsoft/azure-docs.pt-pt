---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 65f6c239f34775efff6a2ea2e399064a7702606a
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664657"
---
![Documentação Dsv3](media/vm-disk-performance/dsv3-documentation.jpg)

A produção máxima de disco **não acoched** é o limite máximo de armazenamento predefinido que a máquina virtual é capaz de manusear. O limite máximo de produção **em cache** é um limite separado quando ativa o cache do anfitrião. Ativar o cache do anfitrião pode ser feito ao criar a sua máquina virtual e a anexar discos. Também pode ajustar-se para ligar e desligar o anfitrião, cachendo os seus discos num VM existente:

![Caching hospedeiro](media/vm-disk-performance/host-caching.jpg)

O cache do anfitrião pode ser ajustado de modo a corresponder aos seus requisitos de carga de trabalho para cada disco. Pode definir o seu caching do anfitrião para ser Read-Only para cargas de trabalho que apenas fazem operações de leitura e Leia/Escreva para cargas de trabalho que façam um balanço de operações de leitura e escrita. Se a sua carga de trabalho não seguir nenhum desses padrões, infelizmente não poderá usar o caching hospedeiro. 

Vamos continuar com um exemplo com a nossa Standard_D8s_v3 máquina virtual. Só que desta vez, vamos permitir o cache do anfitrião nos discos e agora o limite de IOPS do VM é de 16.000 IOPS. Anexados ao VM estão três discos P30 subjacentes que aguentam 5.000 IOPS.

Configurar:
- Standard_D8s_v3 
    - IOPS em cache: 16.000
    - IOPS sem cocó: 12.800
- Disco P30 OS 
    - IOPS: 5.000
    - Caching do anfitrião ativado 
- 2 Discos de Dados P30
    - IOPS: 5.000
    - Caching do anfitrião ativado

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
    - Caching do anfitrião ativado 
- 2 Discos de dados P30 X 2
    - IOPS: 5.000
    - Caching do anfitrião ativado
- 2 Discos de dados P30 X 2
    - IOPS: 5.000
    - Caching hospedeiro incapacitado

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
- **Percentagem consumida** pelo disco de dados - a percentagem calculada pelos IOPs do disco de dados concluída ao longo dos IOPs do disco de dados fortado. Se este valor estiver a 100%, a sua aplicação em execução será limitada a partir do limite de IOPS do seu disco de dados.
- **Percentagem consumida** pela largura de banda do disco de dados - a percentagem calculada pela produção do disco de dados concluída sobre o rendimento do disco de dados fortado. Se este valor estiver a 100%, a sua aplicação em execução será limitada a partir do limite de largura de banda do seu disco de dados.
- **Percentagem consumida** pelo disco oss - a percentagem calculada pelos IOPs do disco oss concluídos sobre os IOPs do disco OS a provisionados. Se este valor for de 100%, a sua aplicação em execução será limitada ao limite de IOPS do seu disco de SO.
- **Os Discos de Banda consumida Percentagem Consumida** - a percentagem calculada pela produção do disco os concluída sobre a produção do disco de OS fortada. Se este valor estiver a 100%, a sua aplicação em execução será limitada ao limite de largura de banda do seu disco de SO.