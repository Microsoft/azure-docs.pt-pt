---
title: Guia de dimensionamento de cluster de consulta interativa HDInsight (LLAP)
description: Guia de tamanho LLAP
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: a9b86f09ade0d437436779ef3e4a17fcdede2cf0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664964"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Guia de dimensionamento de consulta interativa Azure HDInsight (Hive LLAP)

Este documento descreve o dimensionamento do Cluster de Consulta Interativa HDInsight (cluster Hive LLAP) para uma carga de trabalho típica para obter um desempenho razoável. Note que as recomendações fornecidas neste documento são orientações genéricas e cargas de trabalho específicas podem necessitar de   afinação específica.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Tipos de VM padrão azure para cluster de consulta interativa HDInsight (LLAP)**

| Tipo de nó      | Instância | Tamanho     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vcpus, 56 GB RAM, 400 GB SSD   |
| Trabalhador   | **D14 v2**        | **16 vcpus, 112 GB RAM, 800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 vcpus, 8-GB RAM, 40 GB SSD       |

***Nota: Todos os valores de configurações recomendados são baseados no nó de trabalhador do tipo D14 v2***  

### <a name="configuration"></a>**Configuração:**    
| Chave de Configuração      | Valor recomendado  | Descrição |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | Memória total dada, em MB, para todos os recipientes YARN em um nó | 
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | A atribuição máxima de cada pedido de contentor no RM, em MBs. Pedidos de memória superiores a este valor não vão ter efeito |
| yarn.scheduler.maximum-allocation-vcores | 12 |O número máximo de núcleos de CPU para cada pedido de contentor no Gestor de Recursos. Pedidos superiores a este valor não vão ter efeito. |
| yarn.nodemanager.resource.cpu-vcores | 12 | Número de núcleos de CPU por NodeManager que podem ser atribuídos para contentores. |
| yarn.scheduler.capacity.root.llap.capacity | 80 (%) | Atribuição de capacidade de ARN para fila de llap  |
| tez.am.resource.memory.mb | 4096 (MB) | A quantidade de memória em MB a ser usada pelo tez AppMaster |
| hive.server2.tez.sessions.per.default.queue | <number_of_worker_nodes> |O número de sessões para cada fila nomeada nas filas hive.server2.tez.default.default.queues. Este número corresponde ao número de coordenadores de consulta (Tez AMs) |
| hive.tez.container.size.size | 4096 (MB) | Tamanho especificado do recipiente Tez em MB |
| hive.llap.daemon.num.executores | 12 | Número de executores por daemon LLAP | 
| hive.llap.io.threadpool.size | 12 | Tamanho da piscina de rosca para executores |
| hive.llap.daemon.yarn.container.mb | 77824 (MB) | Memória total em MB utilizada por daemons LLAP individuais (Memória por daemon)
| hive.llap.io.memory.tamanho.tamanho | 235520 (MB) | Tamanho de cache em MB por llap daemon desde que a cache SSD esteja ativada |
| hive.auto.converte.join.noconditionaltask.size | 2048 (MB) | tamanho da memória em MB para fazer Mapa Juntar |

### <a name="llap-daemon-size-estimations"></a>**Estimativas de tamanho llap Daemon:**  

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. Determinar a atribuição total da memória do ARN para todos os recipientes num nó**    
Configuração: ***yarn.nodemanager.resource.memory-mb***  

Este valor indica uma soma máxima de memória em MB que pode ser utilizada pelos recipientes YARN em cada nó. O valor especificado deve ser inferior à quantidade total de memória física nesse nó.   
Memória total para todos os recipientes de ARN num nó = [Memória física total] – [ memória para OS + Outros serviços]  
Defina este valor para ~90% do tamanho de RAM disponível.  
Para D14 v2, o valor recomendado é **de 102400 MB**. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. Determinação da quantidade máxima de memória por pedido de contentor de ARN**  
Configuração: ***yarn.scheduler.maximum-allocation-mb***

Este valor indica a atribuição máxima para cada pedido de contentor no Gestor de Recursos, em MB. Pedidos de memória superiores ao valor especificado não produzirão efeito. O Gestor de Recursos pode dar memória aos recipientes em incrementos de *arn.scheduler.minimum-allocation-mb* e não pode exceder o tamanho especificado por *fio.scheduler.maximum-allocation-mb*. O valor especificado não deve ser superior à memória total dada a todos os recipientes no nó especificado por *arn.nodemanager.resource.memory-mb*.    
Para os nós de trabalhador esportivo D14 v2, o valor recomendado é **de 102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. Determinação da quantidade máxima de vcores por pedido de contentor yARN**  
Configuração: ***yarn.scheduler.maximum-allocation-vcores***  

Este valor indica o número máximo de núcleos de CPU virtuais para cada pedido de contentor no Gestor de Recursos. Pedir um maior número de vcores do que este valor não vai ter efeito. É uma propriedade global do programador da ARN. Para o recipiente de daemon LLAP, este valor pode ser definido para 75% do total de vcores disponíveis. Os restantes 25% devem ser reservados para NodeManager, DataNode e outros serviços em funcionamento nos nós dos trabalhadores.  
Existem 16 vcores em VMs D14 v2 e 75% do total de 16 vcores podem ser usados pelo recipiente de daemon LLAP.  
Para D14 v2, o valor recomendado é **de 12**.  

#### <a name="4-number-of-concurrent-queries"></a>**4. Número de consultas simultâneas**  
Configuração: ***hive.server2.tez.sessions.per.predefinido.queue***

Este valor de configuração determina o número de sessões tez que podem ser lançadas em paralelo. Estas sessões de Tez serão lançadas para cada uma das filas especificadas por "hive.server2.tez.predefinido.queues". Corresponde ao número de AMs tez (Coordenadores de Consulta). Recomenda-se que seja o mesmo que o número de nós dos trabalhadores. O número de AMs tez pode ser maior do que o número de nós dada LLAP. A principal responsabilidade da Am tez é coordenar a execução da consulta e atribuir fragmentos de plano de consulta aos daemons llap correspondentes para execução. Mantenha este valor como múltiplos de vários nós daLLAP daemon para obter uma maior proscção.  

O cluster HDInsight padrão tem quatro daemons LLAP em execução em quatro nós de trabalhador, pelo que o valor recomendado é **de 4**.  

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. Contentor tez e tez aplicação tamanho principal**    
Configuração: ***tez.am.resource.memory.mb, hive.tez.container.size***  

*tez.am.resource.memory.mb* - define o tamanho mestre da aplicação Tez.  
O valor recomendado é de **4096 MB**.
   
*hive.tez.container.size* - define a quantidade de memória dada para o recipiente Tez. Este valor deve ser fixado entre o tamanho mínimo do contentor YARN *(yarn.scheduler.minimum-allocation-mb)* e o tamanho máximo do contentor YARN *(yarn.scheduler.maximum-allocation-mb).* Os executores da LLAP usam este valor para limitar o uso da memória por executor.  
O valor recomendado é de **4096 MB**.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. Atribuição da capacidade da fila LLAP**   
Configuração: ***yarn.scheduler.capacity.root.llap.capacity***  

Este valor indica uma percentagem de capacidade dada à fila de llap. As dotações de capacidade podem ter valores diferentes para diferentes cargas de trabalho, dependendo da configuração das filas de Fios. Se a sua carga de trabalho for apenas para leitura, então a definição até 90% da capacidade deve funcionar. No entanto, se a sua carga de trabalho for misturada de operações de atualização/eliminação/fusão utilizando tabelas geridas, é aconselhável dar 80% da capacidade para a fila llap. A capacidade restante de 20% pode ser utilizada por outras tarefas, como a compactação, etc. para alocar contentores da fila padrão. Assim, as tarefas na fila padrão não privarão os recursos da ARN.    
Para os nós dos trabalhadores D14v2, o valor recomendado para a fila de llap é **de 80**.   
(Para cargas de trabalho de leitura, pode ser aumentado até 90 conforme adequado.)  

#### <a name="7-llap-daemon-container-size"></a>**7. Tamanho do recipiente de daemon LLAP**    
Configuração: ***hive.llap.daemon.yarn.container.mb***  
   
O daemon LLAP é executado como um recipiente YARN em cada nó de trabalhador. O tamanho total da memória do recipiente daemon LLAP depende dos seguintes fatores,    
*  Configurações do tamanho do contentor YARN (yarn.scheduler.minimum-allocation-mb, yarn.scheduler.maximum-allocation-mb, yarn.nodemanager.resource.memory-mb)
*  Número de AMs tez em um nó
*  Memória total configurada para todos os recipientes em um nó e capacidade de fila LLAP  

A memória necessária pela Tez Application Masters (Tez AM) pode ser calculada da seguinte forma.  
Para o cluster HDInsight Interactive, por padrão, existe um Tez AM por nó de trabalhador que funciona como um coordenador de consulta. O número de AMs tez pode ser configurado com base numa série de consultas simultâneas a serem servidas.
Recomenda-se ter 4 GB de memória por Tez AM.

Tez AM memória por nó = [ ceil (número de Tez AMs / Número de nós daemon LLAP) ] x [ Tamanho do recipiente Tez AM ]  
Para D14 v2, a configuração padrão tem quatro AMs Tez e quatro nós de daemon LLAP.  
Tez AM memória por nó = (ceil(4/4) x 4 GB) = 4 GB

Memória Total disponível para fila LLAP por nó de trabalhador pode ser calculado da seguinte forma: Este valor depende da quantidade total de memória disponível para todos os recipientes de ARN num*nó(yarn.nodemanager.resource.memory-mb*) e da percentagem de capacidade configurada para a fila llap *(yarn.scheduler.capacity.root.llap.capacity).*  
Memória total para fila LLAP no nó do trabalhador = Memória total disponível para todos os recipientes de ARN num nó x Percentagem de capacidade para fila de llap.  
Para D14 v2, este valor é [ 100 GB x 0,80 ] = 80 GB.

O tamanho do recipiente de daemon LLAP é calculado da seguinte forma;

**Tamanho do recipiente llap daemon = [ Memória total disponível para fila LLAP ] – [ Memória Tez AM por nó ]**  
    
Para o nó de trabalhador d14 v2, HDI 4.0 - o valor recomendado é (80 GB - 4 GB)) = **76 GB**   
(Para o HDI 3.6, o valor recomendado é de **74 GB** porque deve reservar mais ~2 GB para slider AM.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. Determinação do número de executores por daemon LLAP**  
Configuração: ***hive.llap.daemon.num.executores,*** ***hive.llap.io.threadpool.size***

***hive.llap.daemon.num.executores:***   
Esta configuração controla o número de executores que podem executar tarefas paralelamente por daemon LLAP. Este valor depende do número de vcores, da quantidade de memória dada por executor e da quantidade de memória total disponível para o daemon LLAP. Normalmente, gostaríamos que este valor fosse o mais próximo possível do número de vcores.
Existem 16 vcores em VMs D14 v2. No entanto, nem todos os vcores podem ser tomados porque outros serviços como NodeManager, DataNode, Metrics Monitor, etc. também precisam de parte de vcores disponíveis. 

Se necessitar de ajustar o número de executores, recomenda-se que considere 4 GB de memória por executor conforme especificado pela *hive.tez.container.size* e certifique-se de que a memória total necessária para todos os executores não exceda a memória total disponível para o recipiente daemon LLAP.  
Este valor pode ser configurado até 75% do total de vcores disponíveis nesse nó.  
Para D14 v2, o valor recomendado é (.75 X 16) = **12**

***hive.llap.io.threadpool.size***:   
Este valor especifica o tamanho da piscina de rosca para executores. Uma vez que os executores são fixados como especificado, será o mesmo que o número de executores por daemon LLAP.   
Para D14 v2, o valor recomendado é **de 12**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. Determinar o tamanho do cache de daemon LLAP**  
Configuração: ***hive.llap.io.memory.size***

A memória do recipiente llap daemon consiste em componentes seguintes;
*  Sala da cabeça
*  Memória heap usada por executores (Xmx)
*  Cache de memória por daemon (o seu tamanho de memória off-heap, não aplicável quando a cache SSD está ativada)
*  Tamanho dos metadados de cache na memória (aplicável apenas quando a cache SSD estiver ativada)

**Tamanho**da sala de cabeceira : Este tamanho indica uma parte da memória off-heap utilizada para a sobrecarga de Java VM (metaspace, empilhadora de fios, estruturas de dados gc, etc.). Geralmente, esta sobrecarga é de cerca de 6% do tamanho da pilha (Xmx). Para estar no lado mais seguro, este valor pode ser calculado como 6% do tamanho total da memória llap daemon.  
Para D14 v2, o valor recomendado é ceil (76 GB x 0,06) ~= **5 GB**.  

**Tamanho da pilha (Xmx)**: É uma quantidade de memória de pilha disponível para todos os executores.
Tamanho total da pilha = Número de executores x 4 GB  
Para D14 v2, este valor é de 12 x 4 GB = **48 GB**  

Se a cache SSD for desativada, a cache de memória é a quantidade de memória que fica após retirar o tamanho da sala de cabeceira e o tamanho heap do tamanho do recipiente llap daemon.

O cálculo do tamanho da cache difere quando a cache SSD está ativada.
Definição de *hive.llap.io.allocator.mmap* = verdadeiro permitirá o cache do SSD.
Quando a cache SSD estiver ativada, alguma parte da memória será usada para armazenar metadados para a cache SSD. Os metadados são armazenados na memória e espera-se que seja ~10% do tamanho da cache SSD.   
Tamanho dos metadados ssd cache na memória = [ LLAP daemon container size ] - [ Sala de cabeça + tamanho heap ]  
Para D14 v2, com HDI 4.0, cache SSD na memória tamanho dos metadados = [ 76 GB ] - [ 5 GB + 48 GB ] = **23 GB**  
Para D14 v2, com HDI 3.6, tamanho de metadados em cache SSD na memória = [ 76 GB ] - [ 5 GB + 48 GB + 2 GB slider ] = **21 GB**

Dado o tamanho da memória disponível para armazenar metadados de cache SSD, podemos calcular o tamanho da cache SSD que pode ser suportada.  
Tamanho dos metadados em memória para cache SSD = 10% do tamanho de Cache SSD       
Tamanho da cache SSD = tamanho dos metadados em memória para cache SSD x 10  

Para D14 v2 e HDI 4.0, o tamanho recomendado de cache SSD = 23 GB x 10 = **230 GB**  
Para D14 v2 e HDI 3.6, o tamanho recomendado de cache SSD = 21 GB x 10 = **210 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. Mapa de ajuste de memória**   
Configuração: ***hive.auto.convert.join.noconditionaltask.size***

Certifique-se de que tem *hive.auto.converte.join.noconditionaltask enabled* para que este parâmetro faça efeito.
Esta configuração permite ao utilizador especificar o tamanho das tabelas que podem caber na memória para fazer a adesão ao Mapa. Se a soma do tamanho de tabelas n-1 ou divisórias para a união n-way for inferior ao valor configurado, a adesão ao Mapa será escolhida. O tamanho da memória do executor LLAP deve ser utilizado para calcular o limiar para conversão automática para Map Join.
Cada executor é assumido ter 4 GB de tamanho heap, mas nem tudo estará disponível para a adesão do Mapa. Algumas memórias de amontoados serão usadas para classificação de tampões, tampão de baralhar, mesas de hash, etc. por outras operações também. Então, você pode dar 50% de 4 GB de memória heap para a adesão ao Mapa.  
Nota: Este valor pode necessitar de ajustes adequados para a sua carga de trabalho. Definir este valor demasiado baixo pode não utilizar a função de conversão automática. E defini-lo demasiado alto pode resultar em exceções de memória ou pausas GC que podem resultar em desempenho adverso.  
Para D14 v2, com memória de 4 GB por executor, é aconselhável definir este valor para **2048 MB**.


#### <a name="next-steps"></a>**Próximos Passos**
Se definir estes valores não resolveu o seu problema, visite um dos seguintes...

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)  

* ##### <a name="other-references"></a>**Outras Referências:**
  * [Configure outras propriedades LLAP](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Configure o tamanho do heap do servidor da Colmeia](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Mapa Junte o dimensionamento da memória para LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Propriedades do motor de execução tez](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [Hive LLAP mergulho profundo](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)  
