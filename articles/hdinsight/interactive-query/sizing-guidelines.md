---
title: Guia interativo de tamanho de cluster de consulta em Azure HDInsight
description: Guia interativo de dimensionamento de consulta no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a81eff1dcf48996c319933aa4dd46170043b943b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664936"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Guia interativo de tamanho de cluster de consulta em Azure HDInsight

Este documento descreve o dimensionamento do cluster de consulta interativa HDInsight (LLAP) para uma carga de trabalho típica para obter um desempenho razoável. As recomendações fornecidas neste documento são genéricas e as cargas de trabalho específicas podem necessitar de afinação específica.

## <a name="default-vm-types-for-interactive-query"></a>Tipos de VM padrão para consulta interativa

| Tipo de nó | Instância | Tamanho |
|---|---|---|
| Head | D13 v2 | 8 VCPUS, 56-GB RAM, 400 GB SSD |
| Trabalhador | D14 v2 | 16 VCPUS, 112-GB RAM, 800 GB SSD |
| ZooKeeper | A4 v2 | 4 VCPUS, 8-GB RAM, 40 GB SSD |

## <a name="recommended-configurations"></a>Configurações recomendadas

Os valores de configurações recomendados baseiam-se no nó de trabalhador do tipo D14 v2.

| Chave | Valor | Descrição |
|---|---|---|
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | Memória total dada, em MB, para todos os recipientes de ARN em um nó. |
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | A atribuição máxima de cada pedido de contentor no RM, em MBs. Pedidos de memória superiores a este valor não vão ter efeito. |
| yarn.scheduler.maximum-allocation-vcores | 12 |O número máximo de núcleos de CPU para cada pedido de contentor no Gestor de Recursos. Pedidos superiores a este valor não vão ter efeito. |
| yarn.scheduler.capacity.root.llap.capacity | 90% | Atribuição de capacidade de ARN para fila LLAP.  |
| hive.server2.tez.sessions.per.default.queue | number_of_worker_nodes |O número de sessões para cada fila nomeada nas filas hive.server2.tez.default.default.queues. Este número corresponde ao número de coordenadores de consulta (Tez AMs). |
| tez.am.resource.memory.mb | 4096 (MB) | A quantidade de memória em MB a ser usada pelo tez AppMaster. |
| hive.tez.container.size.size | 4096 (MB) | Tamanho especificado do recipiente Tez em MB. |
| hive.llap.daemon.num.executores | 12 | Número de executores por daemon LLAP. |
| hive.llap.io.threadpool.size | 12 | Tamanho da piscina para executores. |
| hive.llap.daemon.yarn.container.mb | 86016 (MB) | Memória total em MB usado por daemons LLAP individuais (Memória por daemon).|
| hive.llap.io.memory.tamanho.tamanho | 409600 (MB) | Tamanho de cache em MB por llap daemon desde que a cache SSD esteja ativada. |
| hive.auto.converte.join.noconditionaltask.size | 2048 (MB) | tamanho da memória em MB para fazer Map Join. |

## <a name="llap-daemon-size-estimations"></a>Estimativas de tamanho de daemon LLAP  

### <a name="yarnnodemanagerresourcememory-mb"></a>yarn.nodemanager.resource.memory-mb

Este valor indica uma soma máxima de memória em MB utilizada pelos recipientes YARN em cada nó. Especifica a quantidade de memória que o FIO pode usar neste nó, pelo que este valor deve ser menor do que a memória total desse nó.

Desdefinido este valor = [Memória física total no nó] – [ memória para OS + Outros serviços].

Recomenda-se definir este valor para ~90% da RAM disponível. Para D14 v2, o valor recomendado é **de 102400 MB**.

### <a name="yarnschedulermaximum-allocation-mb"></a>yarn.scheduler.maximum-allocation-mb

Este valor indica a atribuição máxima para cada pedido de contentor no Gestor de Recursos, em MB. Pedidos de memória superiores ao valor especificado não produzirão efeito. O Gestor de Recursos só pode dar memória aos recipientes em incrementos de `yarn.scheduler.minimum-allocation-mb` e não pode exceder o tamanho especificado por `yarn.scheduler.maximum-allocation-mb` . Este valor não deve ser mais do que a memória total dada do nó, que é especificado por `yarn.nodemanager.resource.memory-mb` .

Para os nós de trabalhador esportivo D14 v2, o valor recomendado é **de 102400 MB**

### <a name="yarnschedulermaximum-allocation-vcores"></a>yarn.scheduler.maximum-allocation-vcores

Esta configuração indica o número máximo de núcleos de CPU virtuais para cada pedido de contentor no Gestor de Recursos. Pedir um valor superior a esta configuração não vai ter efeito. Esta configuração é uma propriedade global do programador YARN. Para o recipiente da ememon LLAP, este valor pode ser definido para 75% do total de núcleos virtuais disponíveis (VCORES). Os restantes 25% devem ser reservados para NodeManager, DataNode e outros serviços em funcionamento nos nós dos trabalhadores.  

Para os nós de trabalhadores D14 v2, existem 16 VCORES e 75% dos 16 VCORES podem ser administrados. Assim, o valor recomendado para o recipiente de daemon LLAP é **de 12**.

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>hive.server2.tez.sessions.per.default.queue

Este valor de configuração determina o número de sessões tez que devem ser lançadas paralelamente para cada uma das filas especificadas por `hive.server2.tez.default.queues` . O valor corresponde ao número de AMs tez (Coordenadores de Consulta). Recomenda-se que seja o mesmo que o número de nós operários para ter um Tez AM por nó. O número de AMs tez pode ser maior do que o número de nós dada LLAP. A sua principal responsabilidade é coordenar a execução da consulta e atribuir fragmentos de plano de consulta aos daemons llap correspondentes para execução. É recomendado mantê-lo como um múltiplo de vários nós dada LLAP para obter uma maior entrada.  

O cluster HDInsight padrão tem quatro daemons LLAP em execução em quatro nós de trabalhador, pelo que o valor recomendado é **de 4**.  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>tez.am.resource.memory.mb, hive.tez.container.size

`tez.am.resource.memory.mb`define o tamanho mestre da aplicação Tez.  
O valor recomendado é de **4096 MB**.

`hive.tez.container.size`define a quantidade de memória dada para o recipiente Tez. Este valor deve ser fixado entre o tamanho mínimo do recipiente YARN `yarn.scheduler.minimum-allocation-mb` e o tamanho máximo do contentor yARN. `yarn.scheduler.maximum-allocation-mb`  
Recomenda-se que seja fixado em **4096 MB**.  

Uma regra geral é mantê-la menor do que a quantidade de memória por processador, considerando um processador por recipiente. `Rreserve`memória para o número de AMs tez em um nó antes de dar a memória para llap daemon. Por exemplo, se estiver a utilizar dois AMs Tez (4 GB cada) por nó, dê 82 GB de 90 GB para o daemon LLAP reservando 8 GB para dois AMs Tez.

### <a name="yarnschedulercapacityrootllapcapacity"></a>yarn.scheduler.capacity.root.llap.capacity

Este valor indica uma percentagem de capacidade dada para a fila LLAP. O cluster de consulta interativa HDInsights dá 90% da capacidade total para a fila LLAP e os restantes 10% estão definidos para a fila padrão para outras alocações de contentores.  
Para os nós de trabalhadorD14v2, o valor recomendado é **de 90** para a fila LLAP.

### <a name="hivellapdaemonyarncontainermb"></a>hive.llap.daemon.yarn.container.mb

O tamanho total da memória para daemon LLAP depende dos seguintes componentes:

* Configuração do tamanho do recipiente YARN `yarn.scheduler.maximum-allocation-mb` `yarn.scheduler.maximum-allocation-mb` (, `yarn.nodemanager.resource.memory-mb` , )

* Memória heap usada por executores (Xmx)

    A sua quantidade de RAM disponível após tirar o tamanho da sala de cabeceira.  
    Para D14 v2, HDI 4.0 - este valor é (86 GB - 6 GB) = 80 GB  
    Para D14 v2, HDI 3.6 - este valor é (84 GB - 6 GB) = 78 GB

* Cache de memória off-heap por daemon (hive.llap.io.memory.size)

* Sala da frente

    É uma porção de memória off-heap usada para a sobrecarga de Java VM (metaspace, pilha de fios, estruturas de dados gc, e assim por diante). Esta porção é observada como sendo de cerca de 6% do tamanho da pilha (Xmx). Para estar no lado mais seguro, pode ser calculado como 6% do tamanho total da memória llap daemon. Como é possível quando a cache SSD estiver ativada, permitirá que o daemon LLAP utilize todo o espaço disponível na memória para ser usado apenas para pilha.  
    Para D14 v2, o valor recomendado é ceil (86 GB x 0,06) ~= **6 GB**.  

Memória por daemon = [Tamanho da cache na memória] + [Tamanho da pilha] + [Cabeceira].

Pode ser calculado da seguinte forma:

Tez AM memória por nó = [ (Número de Tez AMs/Número de nós daemon LLAP) * Tez AM tamanho ].
Tamanho do recipiente llap daemon = [ 90% da memória do recipiente arn max ] – [ Memória Tez AM por nó].

Para o nó de trabalhador d14 v2, HDI 4.0 - o valor recomendado é (90 - (1/1 * 4 GB)) = **86 GB**.
(Para o HDI 3.6, o valor recomendado é de **84 GB** porque deve reservar ~2 GB para slider AM.)  

### <a name="hivellapiomemorysize"></a>hive.llap.io.memory.tamanho.tamanho

Esta configuração é a quantidade de memória disponível como cache para daemon LLAP. Os daemons LLAP podem usar SSD como cache. Definição = verdadeira permitirá o `hive.llap.io.allocator.mmap` cache de SSD. O D14 v2 vem com ~800 GB de SSD e o cache sSD é ativado por padrão para cluster de consulta interativa (LLAP). Está configurado para usar 50% do espaço SSD para cache off-heap.

Para D14 v2, o valor recomendado é de **409600 MB**.  

Para outros VMs, sem caching SSD habilitado, é benéfico dar uma porção de RAM disponível para o caching LLAP para obter um melhor desempenho. Ajuste o tamanho total da memória para o daemon LLAP da seguinte forma:  

Memória total de daemon LLAP = [TAMANHO DE cache LLAP] + [Tamanho de pilha] + [Headroom].

É aconselhável ajustar o tamanho da cache e o tamanho da pilha que é melhor para a sua carga de trabalho.  

### <a name="hivellapdaemonnumexecutors"></a>hive.llap.daemon.num.executores

Esta configuração controla o número de executores que podem executar tarefas paralelamente por daemon LLAP. Este valor é um saldo do número de VCORES disponíveis, a quantidade de memória dada por executor, e memória total disponível por daemon LLAP. Normalmente, gostaríamos que este valor fosse o mais próximo possível do número de núcleos.

Para d14 v2, existem 16 VCORES disponíveis, no entanto nem todos os VCORES podem ser administrados. Os nós dos trabalhadores também gerem outros serviços como NodeManager, DataNode e Metrics Monitor, que precisa de alguma parte do VCORES disponível. Este valor pode ser configurado até 75% do total de VCORES disponíveis nesse nó.

Para D14 v2, o valor recomendado é (.75 X 16) = **12**

Recomenda-se que reserve ~6 GB de espaço de pilha por executor. Ajuste o seu número de executores com base no tamanho disponível do daemon LLAP e no número de VCORES disponíveis por nó.  

### <a name="hivellapiothreadpoolsize"></a>hive.llap.io.threadpool.size

Este valor especifica o tamanho da piscina de rosca para executores. Uma vez que os executores são fixados como especificado, será o mesmo que o número de executores por daemon LLAP.

Para D14 v2, é aconselhável definir este valor para **12**.

Esta configuração não pode exceder o `yarn.nodemanager.resource.cpu-vcores` valor.

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>hive.auto.converte.join.noconditionaltask.size

Certifique-se de que `hive.auto.convert.join.noconditionaltask` ativou este parâmetro para fazer efeito. Esta configuração permite ao utilizador especificar o tamanho das tabelas que podem caber na memória para fazer a adesão ao Mapa. Se a soma do tamanho de n-1 da `tables/partitions` união n-way for inferior ao valor configurado, a adesão ao Mapa será escolhida. O tamanho da memória do executor LLAP deve ser utilizado para calcular o limiar para conversão automática para Map Join.

Para D14 v2, é aconselhável definir este valor para **2048 MB**.

Recomendamos que ajuste este valor adequado para a sua carga de trabalho, uma vez que definir este valor demasiado baixo pode não utilizar a função de conversão automática. Defini-lo demasiado alto pode resultar em pausas gc, o que pode afetar negativamente o desempenho da consulta.

## <a name="next-steps"></a>Passos seguintes

* [Diretrizes de gateway](gateway-best-practices.md)
* [Desmistificar a afinação da memória de Apache Tez - Passo a passo](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [Mapa Junte o dimensionamento da memória para llap](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP - uma visão geral da arquitetura de uma página](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [Hive LLAP mergulho profundo](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
