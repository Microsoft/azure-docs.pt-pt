---
title: Guia de dimensionamento do Cluster de Consultas Interativas HDInsight (LLAP)
description: Guia de dimensionamento LLAP
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: 57eba9cb6a9350ed66ae132e6a021098da2a4810
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533058"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Guia de tamanhos do cluster de consultas interativas Azure HDInsight (Hive LLAP)

Este documento descreve o dimensionamento do HdInsight Interactive Query Cluster (cluster Hive LLAP) para uma carga de trabalho típica para alcançar um desempenho razoável. Note-se que as recomendações fornecidas neste documento são orientações genéricas e que as cargas de trabalho específicas podem necessitar de afinações específicas.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Tipos de VM padrão Azure para cluster de consultas interativas HDInsight (LLAP)**

| Tipo de nó      | Instância | Tamanho     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 VCPUS, 56 GB RAM, 400 GB SSD   |
| Trabalhador   | **D14 v2**        | **16 VCPUS, 112 GB RAM, 800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 VCPUS, 8-GB RAM, 40 GB SSD       |

**_Nota: Todos os valores recomendados de configurações são baseados no nó de trabalhador do tipo D14 v2_* _  

### <a name="_configuration"></a>_ *Configuração:**    
| Chave de Configuração      | Valor recomendado  | Descrição |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | Memória total dada, em MB, para todos os recipientes yarn em um nó | 
| yarn.scheduler.máxima atribuição-mb | 102400 (MB) | A dotação máxima para cada pedido de contentores no RM, em MBs. Pedidos de memória superiores a este valor não vão ter efeito |
| yarn.scheduler.maximum-allocation-vcores | 12 |O número máximo de núcleos de CPU para cada pedido de contentor no Gestor de Recursos. Pedidos superiores a este valor não vão fazer efeito. |
| yarn.nodemanager.resource.cpu-vcores | 12 | Número de núcleos de CPU por NodeManager que podem ser atribuídos para contentores. |
| yarn.scheduler.capacity.root.llap.capacity | 80 (%) | Atribuição de capacidade de YARN para a fila llap  |
| tez.am.resource.memory.mb | 4096 (MB) | A quantidade de memória em MB para ser usada pelo tez AppMaster |
| hive.server2.tez.sessions.per.default.queue | <number_of_worker_nodes> |O número de sessões para cada fila nomeada nas filas hive.server2.tez.default.. Este número corresponde ao número de coordenadores de consulta (Tez AMs) |
| hive.tez.container.tamanho | 4096 (MB) | Tamanho especificado do recipiente Tez em MB |
| cutors hive.llap.daemon.num.exe | 12 | Número de executores por daemon LLAP | 
| hive.llap.io.threadpool.size | 12 | Tamanho da piscina de fio para executores |
| hive.llap.daemon.yarn.container.mb | 77824 (MB) | Memória total em MB usada por daemons LLAP individuais (Memória por daemon)
| hive.llap.io.memory.size | 235520 (MB) | Tamanho de cache em MB por daemon LLAP desde que a cache SSD esteja ativada |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | tamanho da memória em MB para fazer Map Join |

### <a name="llap-daemon-size-estimations"></a>**Estimativas do tamanho llap Daemon:**  

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. Determinação da atribuição total da memória de YARN para todos os contentores num nó**    
Configuração: * *_yarn.nodemanager.resource.memory-mb_* _  

Este valor indica uma soma máxima de memória em MB que pode ser utilizada pelos recipientes YARN em cada nó. O valor especificado deve ser inferior à quantidade total de memória física nesse nó.   
Memória total para todos os recipientes de YARN num nó = [Memória física Total] – [ memória para OS + Outros serviços ]  
Desaponma este valor a ~90% do tamanho disponível de RAM.  
Para D14 v2, o valor recomendado é _*102400 MB**. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. Determinação da quantidade máxima de memória por pedido de contentor YARN**  
Configuração: * *_fios.scheduler.maximum-allocation-mb_* _

Este valor indica a atribuição máxima de cada pedido de contentor no Gestor de Recursos, em MB. Pedidos de memória superiores ao valor especificado não produzirão efeito. O Gestor de Recursos pode dar memória aos recipientes em incrementos de _yarn.scheduler.minimum-allocation-mb* e não pode exceder o tamanho especificado por *yarn.scheduler.maximum-allocation-mb* . O valor especificado não deve ser superior à memória total dada para todos os recipientes do nó especificado por *yarn.nodemanager.resource.memory-mb* .    
Para os nóns de trabalhadores D14 v2, o valor recomendado é **de 102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. Determinação da quantidade máxima de vcores por pedido de contentor YARN**  
Configuração: * *_yarn.scheduler.maximum-allocation-vcores_* _  

Este valor indica o número máximo de núcleos de CPU virtuais para cada pedido de contentor no Gestor de Recursos. Pedir um número mais elevado de vcores do que este valor não vai fazer efeito. É uma propriedade global do programador YARN. Para o recipiente daemon LLAP, este valor pode ser definido para 75% do total de vcores disponíveis. Os restantes 25% deverão ser reservados ao NodeManager, DataNode e outros serviços em execução nos nós dos trabalhadores.  
Existem 16 vcores em D14 v2 VMs e 75% do total de 16 vcores podem ser usados pelo recipiente daemon LLAP.  
Para D14 v2, o valor recomendado é _*12**.  

#### <a name="4-number-of-concurrent-queries"></a>**4. Número de consultas simultâneas**  
Configuração: * *_hive.server2.tez.sessions.per.default.queue_* _

Este valor de configuração determina o número de sessões de Tez que podem ser lançadas em paralelo. Estas sessões de Tez serão lançadas para cada uma das filas especificadas por "hive.server2.tez.default.queues". Corresponde ao número de Tez AMs (Coordenadores de Consulta). Recomenda-se que seja o mesmo que o número de nós operários. O número de Tez AMs pode ser superior ao número de nós de daemon LLAP. A responsabilidade principal da Tez AM é coordenar a execução de consultas e atribuir fragmentos de plano de consulta aos correspondentes daemons LLAP para execução. Mantenha este valor como múltiplo de vários nós de daemon LLAP para obter uma produção mais elevada.  

O cluster HDInsight padrão tem quatro daemons LLAP em execução em quatro nós de trabalhadores, por isso o valor recomendado é _*4***  

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. Tamanho principal do recipiente Tez e aplicação Tez**    
Configuração: * *_tez.am.resource.memory.mb, hive.tez.container.size_* _  

_tez.am.resource.memory.mb* - define o tamanho principal da aplicação Tez.  
O valor recomendado é **de 4096 MB.**
   
*hive.tez.container.size* - define a quantidade de memória dada para o recipiente Tez. Este valor deve ser definido entre o tamanho mínimo do contentor YARN *(yarn.scheduler.minimum-allocation-mb)* e o tamanho máximo do recipiente YARN(yarn.scheduler.maximum-allocation-mb ). *yarn.scheduler.maximum-allocation-mb* Os executores da deemon LLAP utilizam este valor para limitar o uso da memória por executor.  
O valor recomendado é **de 4096 MB.**  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. Atribuição da capacidade da fila LLAP**   
Configuração: * *_yarn.scheduler.capacity.root.llap.capacity_* _  

Este valor indica uma percentagem da capacidade dada à fila llap. As alocações de capacidade podem ter valores diferentes para diferentes cargas de trabalho, dependendo da configuração das filas yarn. Se a sua carga de trabalho for apenas operações de leitura, então coloque-a até 90% da capacidade deve funcionar. No entanto, se a sua carga de trabalho for mistura de operações de atualização/eliminação/fusão utilizando tabelas geridas, recomenda-se dar 80% da capacidade para a fila llap. A capacidade restante de 20% pode ser utilizada por outras tarefas, como a compactação, etc. para alocar contentores da fila predefinido. Assim, as tarefas na fila padrão não privarão os recursos de YARN.    
Para os nós operários D14v2, o valor recomendado para a fila llap é _*80**.   
(Para apenas cargas de trabalho, pode ser aumentada até 90 conforme adequado.)  

#### <a name="7-llap-daemon-container-size"></a>**7. Tamanho do contentor do daemon LLAP**    
Configuração: * *_hive.llap.daemon.yarn.container.mb_* _  
   
O daemon LLAP é gerido como um contentor YARN em cada nó de trabalhador. O tamanho total da memória do recipiente de daemon LLAP depende dos seguintes fatores,    
_ Configurações do tamanho do contentor YARN (yarn.scheduler.minimum-allocation-mb, yarn.scheduler.maximum-allocation-mb, yarn.nodemanager.resource.memory-mb)
*  Número de Tez AMs em um nó
*  Memória total configurada para todos os recipientes em um nó e capacidade de fila LLAP  

A memória necessária pela Tez Application Masters (Tez AM) pode ser calculada da seguinte forma.  
Para o cluster HDInsight Interactive, por padrão, existe um nó Tez AM por trabalhador que funciona como coordenador de consultas. O número de Tez AMs pode ser configurado com base numa série de consultas simultâneas a serem servidas.
Recomenda-se ter 4 GB de memória por Tez AM.

Tez AM memória por nó = [ ceil (número de Tez AMs / Número de nós de daemon LLAP) ] x [ tamanho do recipiente Tez AM ]  
Para D14 v2, a configuração padrão tem quatro Tez AMs e quatro nóns de daemon LLAP.  
Tez AM memória por nó = (ceil(4/4) x 4 GB) = 4 GB

A memória total disponível para a fila LLAP por nó de trabalhador pode ser calculada da seguinte forma: Este valor depende da quantidade total de memória disponível para todos os recipientes YARN num *nó(yarn.nodemanager.resource.memory-mb)* e da percentagem de capacidade configurada para a fila llap(yarn.scheduler.capacity.root.llap.capacity). *yarn.scheduler.capacity.root.llap.capacity*  
Memória total para a fila LLAP no nó do trabalhador = Memória total disponível para todos os recipientes YARN num nó x Percentagem de capacidade para a fila llap.  
Para D14 v2, este valor é [ 100 GB x 0,80 ] = 80 GB.

O tamanho do contentor do daemon LLAP é calculado da seguinte forma;

**Tamanho do recipiente do daemon LLAP = [ Memória total disponível para a fila LLAP ] – [ Memória Tez AM por nó ]**  
    
Para o nó de trabalhador D14 v2, HDI 4.0 - o valor recomendado é (80 GB - 4 GB)) = **76 GB**   
(Para HDI 3.6, o valor recomendado é **de 74 GB** porque deve reservar ~2 GB adicional para o slider AM.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. Determinar o número de executores por daemon LLAP**  
Configuração: * *_hive.llap.daemon.num.executors_* _, _*_hive.llap.io.threadpool.size_*_

_*_hive.llap.daemon.num.executors:_*_   
Esta configuração controla o número de executores que podem executar tarefas em paralelo por daemon LLAP. Este valor depende do número de vcores, da quantidade de memória dada por executor e da quantidade total de memória disponível para o daemon LLAP. Normalmente, gostaríamos que este valor fosse o mais próximo possível do número de vcores.
Existem 16 vcores em D14 v2 VMs. No entanto, nem todos os vcores podem ser tomados porque outros serviços como NodeManager, DataNode, Metrics Monitor etc. também precisam de uma parte dos vcores disponíveis. 

Se precisar de ajustar o número de executores, recomenda-se que considere 4 GB de memória por executor conforme especificado por _hive.tez.container.size* e certifique-se de que a memória total necessária para todos os executores não exceda a memória total disponível para o recipiente daemon LLAP.  
Este valor pode ser configurado até 75% do total de vcores disponíveis nesse nó.  
Para D14 v2, o valor recomendado é (.75 X 16) = **12**

**_hive.llap.io.threadpool.size_*_: Este valor especifica o tamanho da piscina de fio para executores. Uma vez que os executores são fixados conforme especificado, será o mesmo que o número de executores por daemon LLAP. Para D14 v2, o valor recomendado é _* 12** .

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. Determinar o tamanho da cache do daemon LLAP**  
Configuração: * *_hive.llap.io.memory.size_* _

A memória do contentor do daemon LLAP consiste em seguintes componentes; _ Sala da cabeça
*  Memória de heap usada por executores (Xmx)
*  Cache de memória por daemon (o seu tamanho de memória off-heap, não aplicável quando a cache SSD está ativada)
*  Tamanho dos metadados de cache na memória (aplicável apenas quando a cache SSD estiver ativada)

**Tamanho da sala** da cabeça : Este tamanho indica uma parte da memória off-heap usada para a sobrecarga java VM (metaspace, pilha de fios, estruturas de dados gc, etc.). Geralmente, esta sobrecarga é de cerca de 6% do tamanho da pilha (Xmx). Para estar no lado mais seguro, este valor pode ser calculado como 6% do tamanho total da memória da DEEMON LLAP.  
Para D14 v2, o valor recomendado é ceil (76 GB x 0,06) ~= **5 GB** .  

**Tamanho da pilha (Xmx)** : É uma quantidade de memória de pilha disponível para todos os executores.
Tamanho total da pilha = Número de executores x 4 GB  
Para D14 v2, este valor é de 12 x 4 GB = **48 GB**  

Se a cache SSD for desativada, a cache na memória é a quantidade de memória que é deixada depois de retirar o tamanho da sala de cabeceira e o tamanho do heap do tamanho do recipiente do daemon LLAP.

O cálculo do tamanho da cache difere quando a cache SSD está ativada.
Configuração *da colmeia.llap.io.allocator.mmap* = verdadeiro permitirá o caching SSD.
Quando a cache SSD estiver ativada, alguma parte da memória será usada para armazenar metadados para a cache SSD. Os metadados são armazenados na memória e espera-se que seja ~10% do tamanho da cache SSD.   
Tamanho dos metadados de cache na memória SSD cache = [tamanho do recipiente de daemon LLAP ] - [ Sala da cabeça + tamanho da pilha ]  
Para D14 v2, com HDI 4.0, tamanho de metadados de cache SSD na memória = [ 76 GB ] - [ 5 GB + 48 GB ] = **23 GB**  
Para D14 v2, com HDI 3.6, tamanho de metadados de cache SSD na memória = [ 76 GB ] - [ 5 GB + 48 GB + 2 GB slider ] = **21 GB**

Dado o tamanho da memória disponível para armazenar metadados de cache SSD, podemos calcular o tamanho da cache SSD que pode ser suportada.  
Tamanho dos metadados na memória para cache SSD = 10% do tamanho da Cache SSD       
Tamanho da cache SSD = tamanho dos metadados na memória para cache SSD x 10  

Para D14 v2 e HDI 4.0, o tamanho recomendado de cache SSD = 23 GB x 10 = **230 GB**  
Para D14 v2 e HDI 3.6, o tamanho recomendado de cache SSD = 21 GB x 10 = **210 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. Mapa de ajuste Junte memória**   
Configuração: * *_hive.auto.convert.join.noconditionaltask.size_* _

Certifique-se de que tem _hive.auto.convert.join.noconditionaltask* ativado para que este parâmetro produza efeitos.
Esta configuração permite ao utilizador especificar o tamanho das tabelas que podem caber na memória para fazer a junção do Mapa. Se a soma do tamanho das tabelas n-1 ou das divisórias para a junção n-way for inferior ao valor configurado, o mapa será escolhido. O tamanho da memória do executor LLAP deve ser utilizado para calcular o limiar para o autoconverter para Map Join.
Cada executor é assumido como ter 4 GB de tamanho de pilha, mas nem todos estarão disponíveis para a junção do Mapa. Algumas memórias de amontoados serão usadas para classificar tampão, baralhar tampão, mesas de haxixe, etc. por outras operações também. Então, pode dar 50% de 4 GB de memória de heap para a junção do Mapa.  
Nota: Este valor pode necessitar de ajustes adequados à sua carga de trabalho. Definir este valor demasiado baixo pode não utilizar a função de autoconvertida. E defini-lo demasiado alto pode resultar em exceções de memória ou pausas de GC que podem resultar em desempenho adverso.  
Para D14 v2, com memória de 4 GB por executor, recomenda-se definir este valor para **2048 MB** .


#### <a name="next-steps"></a>**Próximos Passos**
Se a definição destes valores não resolveu o seu problema, visite um dos seguintes...

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).  

* ##### <a name="other-references"></a>**Outras Referências:**
  * [Configure outras propriedades LLAP](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Configure o tamanho da pilha do servidor Hive](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Mapa juntar tamanho de memória para LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Propriedades do motor de execução tez](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [Mergulho profundo da Colmeia LLAP](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)