---
title: Guia de dimensionamento de agrupamento de consultas interativas em Azure HDInsight
description: Guia de tamanhos de consulta interativa em Azure HDInsight
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a7baa9340a1f0a99b94bfcbe535c73d0b502e2a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933056"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Guia de dimensionamento de agrupamento de consultas interativas em Azure HDInsight

Este documento descreve o dimensionamento do cluster de consultas interativas HDInsight (LLAP) para uma carga de trabalho típica para obter um desempenho razoável. As recomendações fornecidas neste documento são genéricas e as cargas de trabalho específicas podem necessitar de afinação específica.

## <a name="default-vm-types-for-interactive-query"></a>Tipos de VM predefinidos para consulta interativa

| Tipo de nó | Instância | Tamanho |
|---|---|---|
| Head | D13 v2 | 8 VCPUS, 56-GB RAM, 400 GB SSD |
| Trabalhador | D14 v2 | 16 VCPUS, 112-GB RAM, 800 GB SSD |
| ZooKeeper | A4 v2 | 4 VCPUS, 8-GB RAM, 40 GB SSD |

## <a name="recommended-configurations"></a>Configurações recomendadas

Os valores recomendados de configurações baseiam-se no nó de trabalhador do tipo D14 v2.

| Chave | Valor | Descrição |
|---|---|---|
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | Memória total dada, em MB, para todos os recipientes de YARN num nó. |
| yarn.scheduler.máxima atribuição-mb | 102400 (MB) | A dotação máxima para cada pedido de contentores no RM, em MBs. Pedidos de memória superiores a este valor não vão fazer efeito. |
| yarn.scheduler.maximum-allocation-vcores | 12 |O número máximo de núcleos de CPU para cada pedido de contentor no Gestor de Recursos. Pedidos superiores a este valor não vão fazer efeito. |
| yarn.scheduler.capacity.root.llap.capacity | 90% | Atribuição de capacidade de YARN para a fila LLAP.  |
| hive.server2.tez.sessions.per.default.queue | number_of_worker_nodes |O número de sessões para cada fila nomeada nas filas hive.server2.tez.default.. Este número corresponde ao número de coordenadores de consulta (Tez AMs). |
| tez.am.resource.memory.mb | 4096 (MB) | A quantidade de memória em MB a ser usada pelo tez AppMaster. |
| hive.tez.container.tamanho | 4096 (MB) | Tamanho especificado do recipiente Tez em MB. |
| cutors hive.llap.daemon.num.exe | 12 | Número de executores por daemon LLAP. |
| hive.llap.io.threadpool.size | 12 | O tamanho da piscina de rosca para executores. |
| hive.llap.daemon.yarn.container.mb | 86016 (MB) | Memória total em MB utilizada por daemons LLAP individuais (Memória por daemon).|
| hive.llap.io.memory.size | 409600 (MB) | Tamanho de cache em MB por daemon LLAP desde que a cache SSD esteja ativada. |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | tamanho da memória em MB para fazer Map Join. |

## <a name="llap-daemon-size-estimations"></a>Estimativas do tamanho do daemon LLAP  

### <a name="yarnnodemanagerresourcememory-mb"></a>yarn.nodemanager.resource.memory-mb

Este valor indica uma soma máxima de memória em MB utilizada pelos recipientes YARN em cada nó. Especifica a quantidade de memória que o YARN pode usar neste nó, pelo que este valor deve ser inferior à memória total desse nó.

Desa esta avaliação = [Memória física total no nó] – [ memória para OS + Outros serviços ].

Recomenda-se definir este valor para ~90% da RAM disponível. Para D14 v2, o valor recomendado é **de 102400 MB.**

### <a name="yarnschedulermaximum-allocation-mb"></a>yarn.scheduler.máxima atribuição-mb

Este valor indica a atribuição máxima de cada pedido de contentor no Gestor de Recursos, em MB. Pedidos de memória superiores ao valor especificado não produzirão efeito. O Gestor de Recursos só pode dar memória aos recipientes em incrementos `yarn.scheduler.minimum-allocation-mb` de e não pode exceder o tamanho especificado por `yarn.scheduler.maximum-allocation-mb` . Este valor não deve ser mais do que a memória total dada do nó, que é especificado por `yarn.nodemanager.resource.memory-mb` .

Para os nóns de trabalhadores D14 v2, o valor recomendado é **de 102400 MB**

### <a name="yarnschedulermaximum-allocation-vcores"></a>yarn.scheduler.maximum-allocation-vcores

Esta configuração indica o número máximo de núcleos de CPU virtuais para cada pedido de contentor no Gestor de Recursos. Pedir um valor superior a esta configuração não vai fazer efeito. Esta configuração é uma propriedade global do programador YARN. Para o recipiente daemon LLAP, este valor pode ser definido para 75% do total de núcleos virtuais disponíveis (VCORES). Os restantes 25% deverão ser reservados ao NodeManager, DataNode e outros serviços em execução nos nós dos trabalhadores.  

Para os nós operários D14 v2, existem 16 VCORES e 75% de 16 VCORES podem ser administrados. Assim, o valor recomendado para o recipiente de daemon LLAP é **12**.

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>hive.server2.tez.sessions.per.default.queue

Este valor de configuração determina o número de sessões de Tez que devem ser lançadas paralelamente para cada uma das filas especificadas por `hive.server2.tez.default.queues` . O valor corresponde ao número de Tez AMs (Coordenadores de Consulta). Recomenda-se que seja o mesmo que o número de nós operários para ter um Tez AM por nó. O número de Tez AMs pode ser superior ao número de nós de daemon LLAP. A sua principal responsabilidade é coordenar a execução de consultas e atribuir fragmentos de plano de consulta aos correspondentes daemons LLAP para execução. É recomendado mantê-lo como múltiplos de vários nós de daemon LLAP para alcançar uma produção mais alta.  

O cluster HDInsight padrão tem quatro daemons LLAP em execução em quatro nós de trabalhadores, pelo que o valor recomendado é **4**.  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>tez.am.resource.memory.mb, hive.tez.container.size

`tez.am.resource.memory.mb` define o tamanho principal da aplicação Tez.  
O valor recomendado é **de 4096 MB.**

`hive.tez.container.size` define a quantidade de memória dada para o recipiente Tez. Este valor deve ser definido entre o tamanho mínimo do contentor YARN `yarn.scheduler.minimum-allocation-mb` e o tamanho máximo do recipiente YARN. `yarn.scheduler.maximum-allocation-mb`  
Recomenda-se que seja definido para **4096 MB.**  

Uma regra geral é mantê-lo inferior à quantidade de memória por processador, considerando um processador por recipiente. `Rreserve` memória para o número de Tez AMs em um nó antes de dar a memória para o daemon LLAP. Por exemplo, se estiver a utilizar dois Tez AMs (4 GB cada) por nó, dê 82 GB de 90 GB para o daemon LLAP reservando 8 GB para dois Tez AMs.

### <a name="yarnschedulercapacityrootllapcapacity"></a>yarn.scheduler.capacity.root.llap.capacity

Este valor indica uma percentagem da capacidade dada para a fila LLAP. O cluster de consultas interativa HDInsights dá 90% da capacidade total para a fila LLAP e os restantes 10% estão definidos para a fila padrão para outras alocações de contentores.  
Para os nós operários D14v2, o valor recomendado é **de 90** para a fila LLAP.

### <a name="hivellapdaemonyarncontainermb"></a>hive.llap.daemon.yarn.container.mb

O tamanho total da memória do daemon LLAP depende dos seguintes componentes:

* Configuração do tamanho do recipiente YARN `yarn.scheduler.maximum-allocation-mb` (, `yarn.scheduler.maximum-allocation-mb` , `yarn.nodemanager.resource.memory-mb` )

* Memória de heap usada por executores (Xmx)

    Sua quantidade de RAM disponível depois de tirar o tamanho da sala de cabeceira.  
    Para D14 v2, HDI 4.0 - este valor é (86 GB - 6 GB) = 80 GB  
    Para D14 v2, HDI 3.6 - este valor é (84 GB - 6 GB) = 78 GB

* Cache off-heap na memória por daemon (hive.llap.io.memory.size)

* Cabeceira

    É uma parte da memória off-heap usada para a sobrecarga java VM (metaspace, pilha de fios, estruturas de dados gc, e assim por diante). Esta porção é observada como sendo cerca de 6% do tamanho da pilha (Xmx). Para estar no lado mais seguro, pode ser calculado como 6% do tamanho total da memória do daemon LLAP. Como é possível quando a cache SSD está ativada, permitirá que o daemon LLAP utilize todo o espaço disponível na memória para ser usado apenas para pilhas.  
    Para D14 v2, o valor recomendado é ceil (86 GB x 0,06) ~= **6 GB**.  

Memória per daemon = [Tamanho da cache na memória] + [Tamanho da pilha] + [Sala de cabeça].

Pode ser calculado da seguinte forma:

Tez AM memória por nó = [ (Número de Tez AMs/Número de nóns de daemon LLAP) * Tamanho Tez AM ].
Tamanho do recipiente do daemon LLAP = [ 90% da memória do contentor máximo de YARN ] – [ Memória Tez AM por nó ].

Para o nó de trabalhador D14 v2, HDI 4.0 - o valor recomendado é (90 - (1/1 * 4 GB)) = **86 GB**.
(Para HDI 3.6, o valor recomendado é **de 84 GB** porque deve reservar ~2 GB para o slider AM.)  

### <a name="hivellapiomemorysize"></a>hive.llap.io.memory.size

Esta configuração é a quantidade de memória disponível como cache para o daemon LLAP. Os daemons LLAP podem usar sSD como cache. Configuração `hive.llap.io.allocator.mmap` = verdadeiro permitirá o caching SSD. O D14 v2 vem com ~800 GB de SSD e o caching SSD é ativado por padrão para o Cluster de consultainterna (LLAP). Está configurado para usar 50% do espaço SSD para cache fora do monte.

Para D14 v2, o valor recomendado é **de 409600 MB**.  

Para outros VMs, sem caching SSD habilitado, é benéfico dar parte da RAM disponível para o caching LLAP para obter um melhor desempenho. Ajuste o tamanho total da memória para o daemon LLAP da seguinte forma:  

Total de memória de daemon LLAP = [tamanho da cache LLAP] + [Tamanho da pilha] + [Sala de cabeceira].

Recomenda-se ajustar o tamanho da cache e o tamanho da pilha que é mais adequado para a sua carga de trabalho.  

### <a name="hivellapdaemonnumexecutors"></a>cutors hive.llap.daemon.num.exe

Esta configuração controla o número de executores que podem executar tarefas em paralelo por daemon LLAP. Este valor é um saldo de número de VCORES disponível, a quantidade de memória dada por executor, e memória total disponível por daemon LLAP. Normalmente, gostaríamos que este valor fosse o mais próximo possível do número de núcleos.

Para D14 v2, existem 16 VCORES disponíveis, no entanto nem todos os VCORES podem ser dados. Os nós dos trabalhadores também gerem outros serviços como NodeManager, DataNode e Metrics Monitor, que precisa de alguma parte do VCORES disponível. Este valor pode ser configurado até 75% do total de VCORES disponíveis nesse nó.

Para D14 v2, o valor recomendado é (.75 X 16) = **12**

Recomenda-se que reserve ~6 GB de espaço de heap por executor. Ajuste o seu número de executores com base no tamanho disponível do daemon LLAP e no número de VCORES disponíveis por nó.  

### <a name="hivellapiothreadpoolsize"></a>hive.llap.io.threadpool.size

Este valor especifica o tamanho da piscina de fio para executores. Uma vez que os executores são fixados conforme especificado, será o mesmo que o número de executores por daemon LLAP.

Para D14 v2, recomenda-se definir este valor para **12**.

Esta configuração não pode exceder `yarn.nodemanager.resource.cpu-vcores` o valor.

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>hive.auto.convert.join.noconditionaltask.size

Certifique-se de que `hive.auto.convert.join.noconditionaltask` ativou este parâmetro para fazer efeito. Esta configuração permite ao utilizador especificar o tamanho das tabelas que podem caber na memória para fazer a junção do Mapa. Se a soma do tamanho de N-1 da `tables/partitions` junção para n-way for do n-way for inferior ao valor configurado, o mapa será escolhido. O tamanho da memória do executor LLAP deve ser utilizado para calcular o limiar para o autoconverter para Map Join.

Para D14 v2, é recomendado definir este valor para **2048 MB**.

Recomendamos que se ajuste este valor adequado para a sua carga de trabalho, uma vez que a definição deste valor demasiado baixo pode não utilizar uma função de autoconvertes. Defini-lo demasiado alto pode resultar em pausas de GC, o que pode afetar negativamente o desempenho da consulta.

## <a name="next-steps"></a>Passos seguintes

* [Diretrizes gateway](gateway-best-practices.md)
* [Demistificar Apache Tez Memory Afinação - Passo a passo](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [Mapa juntar tamanho de memória para LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP - uma visão geral da arquitetura de uma página](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [Mergulho profundo da Colmeia LLAP](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
