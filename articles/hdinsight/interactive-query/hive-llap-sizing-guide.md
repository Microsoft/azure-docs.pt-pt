---
title: Guia de dimensionamento do Cluster de Consultas Interativas HDInsight (LLAP)
description: Guia de dimensionamento LLAP
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: ca3ba61de13e0e451b43dc9c8ea40db33fed859a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869672"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Guia de tamanhos do cluster de consultas interativas Azure HDInsight (Hive LLAP)

Este documento descreve o dimensionamento do HdInsight Interactive Query Cluster (cluster Hive LLAP) para uma carga de trabalho típica para alcançar um desempenho razoável. Note-se que as recomendações fornecidas neste documento são orientações genéricas e que as cargas de trabalho específicas podem necessitar de afinações específicas.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Tipos de VM padrão Azure para cluster de consultas interativas HDInsight (LLAP)**

| Tipo de nó      | Instância | Tamanho     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 VCPUS, 56 GB RAM, 400 GB SSD   |
| Trabalhador   | **D14 v2**        | **16 VCPUS, 112 GB RAM, 800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 VCPUS, 8-GB RAM, 40 GB SSD       |

***Nota: Todos os valores recomendados são baseados no nó de trabalhador do tipo D14 v2***  

### <a name="configuration"></a>**Configuração:**    
| Chave de Configuração      | Valor recomendado  | Descrição |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | Memória total dada, em MB, para todos os recipientes yarn em um nó | 
| yarn.scheduler.máxima atribuição-mb | 102400 (MB) | A dotação máxima para cada pedido de contentores no RM, em MBs. Pedidos de memória superiores a este valor não vão ter efeito |
| yarn.scheduler.maximum-allocation-vcores | 12 |O número máximo de núcleos de CPU para cada pedido de contentor no Gestor de Recursos. Pedidos superiores a este valor não vão fazer efeito. |
| yarn.nodemanager.resource.cpu-vcores | 12 | Número de núcleos de CPU por NodeManager que podem ser atribuídos para contentores. |
| yarn.scheduler.capacity.root.llap.capacity | 85 (%) | Atribuição de capacidade de YARN para a fila llap  |
| tez.am.resource.memory.mb | 4096 (MB) | A quantidade de memória em MB para ser usada pelo tez AppMaster |
| hive.server2.tez.sessions.per.default.queue | <number_of_worker_nodes> |O número de sessões para cada fila nomeada nas filas hive.server2.tez.default.. Este número corresponde ao número de coordenadores de consulta (Tez AMs) |
| hive.tez.container.tamanho | 4096 (MB) | Tamanho especificado do recipiente Tez em MB |
| cutors hive.llap.daemon.num.exe | 19 | Número de executores por daemon LLAP | 
| hive.llap.io.threadpool.size | 19 | Tamanho da piscina de fio para executores |
| hive.llap.daemon.yarn.container.mb | 81920 (MB) | Memória total em MB usada por daemons LLAP individuais (Memória por daemon)
| hive.llap.io.memory.size | 242688 (MB) | Tamanho de cache em MB por daemon LLAP desde que a cache SSD esteja ativada |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | tamanho da memória em MB para fazer Map Join |

### <a name="llap-architecturecomponents"></a>**Arquitetura/Componentes LLAP:**  

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png " alt-text="'LlAP Arquitetura/Componentes'" border="true":::

### <a name="llap-daemon-size-estimations"></a>**Estimativas do tamanho llap Daemon:** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. Determinação da atribuição total da memória de YARN para todos os contentores num nó**    
Configuração: ***yarn.nodemanager.resource.memory-mb***  

Este valor indica uma soma máxima de memória em MB que pode ser utilizada pelos recipientes YARN em cada nó. O valor especificado deve ser inferior à quantidade total de memória física nesse nó.   
Memória total para todos os recipientes de YARN num nó = (Memória física total – memória para OS + Outros serviços)  
Desaponma este valor a ~90% do tamanho disponível de RAM.  
Para D14 v2, o valor recomendado é **de 102400 MB.** 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. Determinação da quantidade máxima de memória por pedido de contentor YARN**  
Configuração: ***yarn.scheduler.maximum-allocation-mb***

Este valor indica a atribuição máxima de cada pedido de contentor no Gestor de Recursos, em MB. Pedidos de memória superiores ao valor especificado não produzirão efeito. O Gestor de Recursos pode dar memória aos recipientes em incrementos de *yarn.scheduler.minimum-allocation-mb* e não pode exceder o tamanho especificado por *yarn.scheduler.maximum-allocation-mb*. O valor especificado não deve ser superior à memória total dada para todos os recipientes do nó especificado por *yarn.nodemanager.resource.memory-mb*.    
Para os nóns de trabalhadores D14 v2, o valor recomendado é **de 102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. Determinação da quantidade máxima de vcores por pedido de contentor YARN**  
Configuração: ***yarn.scheduler.maximum-allocation-vcores***  

Este valor indica o número máximo de núcleos de CPU virtuais para cada pedido de contentor no Gestor de Recursos. Pedir um número mais elevado de vcores do que este valor não vai fazer efeito. É uma propriedade global do programador YARN. Para o recipiente daemon LLAP, este valor pode ser definido para 75% do total de vcores disponíveis. Os restantes 25% deverão ser reservados ao NodeManager, DataNode e outros serviços em execução nos nós dos trabalhadores.  
Existem 16 vcores em D14 v2 VMs e 75% do total de 16 vcores podem ser usados pelo recipiente daemon LLAP.  
Para D14 v2, o valor recomendado é **12**.  

#### <a name="4-number-of-concurrent-queries"></a>**4. Número de consultas simultâneas**  
Configuração: ***hive.server2.tez.sessions.per.default.queue***

Este valor de configuração determina o número de sessões de Tez que podem ser lançadas em paralelo. Estas sessões de Tez serão lançadas para cada uma das filas especificadas por "hive.server2.tez.default.queues". Corresponde ao número de Tez AMs (Coordenadores de Consulta). Recomenda-se que seja o mesmo que o número de nós operários. O número de Tez AMs pode ser superior ao número de nós de daemon LLAP. A responsabilidade principal da Tez AM é coordenar a execução de consultas e atribuir fragmentos de plano de consulta aos correspondentes daemons LLAP para execução. Mantenha este valor como múltiplo de vários nós de daemon LLAP para obter uma produção mais elevada.  

O cluster HDInsight padrão tem quatro daemons LLAP em execução em quatro nós de trabalhadores, pelo que o valor recomendado é **4**.  

**Ambari UI slider para hive config `hive.server2.tez.sessions.per.default.queue` variável:**

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png " alt-text="'Consultas simultâneas máximas LLAP'" border="true":::

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. Tamanho principal do recipiente Tez e aplicação Tez**    
Configuração: ***tez.am.resource.memory.mb, hive.tez.container.size***  

*tez.am.resource.memory.mb* - define o tamanho principal da aplicação Tez.  
O valor recomendado é **de 4096 MB.**
   
*hive.tez.container.size* - define a quantidade de memória dada para o recipiente Tez. Este valor deve ser definido entre o tamanho mínimo do contentor YARN *(yarn.scheduler.minimum-allocation-mb)* e o tamanho máximo do recipiente YARN(yarn.scheduler.maximum-allocation-mb ). Os executores da deemon LLAP utilizam este valor para limitar o uso da memória por executor.  
O valor recomendado é **de 4096 MB.**  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. Atribuição da capacidade da fila LLAP**   
Configuração: ***yarn.scheduler.capacity.root.llap.capacity***  

Este valor indica uma percentagem da capacidade dada à fila llap. As alocações de capacidade podem ter valores diferentes para diferentes cargas de trabalho, dependendo da configuração das filas yarn. Se a sua carga de trabalho for apenas operações de leitura, então coloque-a até 90% da capacidade deve funcionar. No entanto, se a sua carga de trabalho for mistura de operações de atualização/eliminação/fusão utilizando tabelas geridas, recomenda-se dar 85% da capacidade para a fila llap. A capacidade restante de 15% pode ser utilizada por outras tarefas, como a compactação, etc. para alocar recipientes da fila predefinido. Assim, as tarefas na fila padrão não privarão os recursos de YARN.    

Para os nós operários D14v2, o valor recomendado para a fila llap é **de 85**.     
(Para apenas cargas de trabalho, pode ser aumentada até 90 conforme adequado.)  

#### <a name="7-llap-daemon-container-size"></a>**7. Tamanho do contentor do daemon LLAP**    
Configuração: ***hive.llap.daemon.yarn.container.mb***  
   
O daemon LLAP é gerido como um contentor YARN em cada nó de trabalhador. O tamanho total da memória do recipiente de daemon LLAP depende dos seguintes fatores,    
*  Configurações do tamanho do contentor YARN (yarn.scheduler.minimum-allocation-mb, yarn.scheduler.maximum-allocation-mb, yarn.nodemanager.resource.memory-mb)
*  Número de Tez AMs em um nó
*  Memória total configurada para todos os recipientes em um nó e capacidade de fila LLAP  

A memória necessária pela Tez Application Masters (Tez AM) pode ser calculada da seguinte forma.  
A Tez AM atua como coordenador de consultas e o número de Tez AMs deve ser configurado com base numa série de consultas simultâneas a serem servidas. Teoricamente, podemos considerar um nó Tez AM por trabalhador. No entanto, é possível que possa ver mais do que um Tez AM num nó de trabalhador. Para efeitos de cálculo, assumimos uma distribuição uniforme de Tez AMs em todos os nós llap daemon/nós de trabalhadores.
Recomenda-se ter 4 GB de memória por Tez AM.  

Número de Tez Ams = valor especificado por Hive config ***hive.server2.tez.sessions.per.default.queue***.  
Número de nós de daemon LLAP = especificado por ***num_llap_nodes_for_llap_daemons*** variável ENV em Ambari UI.  
Tamanho do recipiente Tez AM = valor especificado por Tez config ***tez.am.resource.memory.mb***.  

Tez AM memória por nó = **(ceil** **(Número** de Tez AMs **/** Número de nós de daemon LLAP **)** **x** tamanho do recipiente Tez AM **)**  
Para D14 v2, a configuração padrão tem quatro Tez AMs e quatro nóns de daemon LLAP.  
Tez AM memória por nó = (ceil(4/4) x 4 GB) = 4 GB

A memória total disponível para a fila LLAP por nó de trabalhador pode ser calculada da seguinte forma:  
Este valor depende da quantidade total de memória disponível para todos os recipientes DE YARN num *nó(yarn.nodemanager.resource.memory-mb)* e da percentagem de capacidade configurada para a fila llap(yarn.scheduler.capacity.root.llap.capacity).  
Memória total para a fila LLAP no nó do trabalhador = Memória total disponível para todos os recipientes YARN num nó x Percentagem de capacidade para a fila llap.  
Para D14 v2, este valor é (100 GB x 0,85) = 85 GB.

O tamanho do contentor do daemon LLAP é calculado da seguinte forma;

**Tamanho do recipiente do daemon LLAP = (Memória total para fila LLAP em um nome de trabalhador) – (memória Tez AM por nó) - (Tamanho do recipiente do Service Master)**  
Existe apenas um Mestre de Serviço (Mestre de Aplicação para o serviço LLAP) no cluster gerado num dos nós do trabalhador. Para efeitos de cálculo, consideramos um mestre de serviço por nó de trabalhador.  
Para o nó de trabalhador D14 v2, HDI 4.0 - o valor recomendado é (85 GB - 4 GB - 1 GB)) = **80 GB**   
(Para HDI 3.6, o valor recomendado é **de 79 GB** porque deve reservar ~2 GB adicional para o slider AM.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. Determinar o número de executores por daemon LLAP**  
Configuração: ***hive.llap.daemon.num.executors** _, _ *_hive.llap.io.threadpool.size_**

***hive.llap.daemon.num.executors:***   
Esta configuração controla o número de executores que podem executar tarefas em paralelo por daemon LLAP. Este valor depende do número de vcores, da quantidade de memória utilizada por executor e da quantidade total de memória disponível para o recipiente de daemon LLAP.    O número de executores pode ser sobrestimado a 120% dos vcores disponíveis por nó de trabalhador. No entanto, deve ser ajustado se não cumprir os requisitos de memória baseados na memória necessária por executor e no tamanho do recipiente do daemon LLAP.

Cada executor é equivalente a um recipiente Tez e pode consumir 4GB (tamanho do recipiente Tez) de memória. Todos os executores em daemon LLAP partilham a mesma memória. Com o pressuposto de que nem todos os executores executam operações intensivas de memória ao mesmo tempo, pode considerar 75% do tamanho do contentor Tez (4 GB) por executor. Desta forma, pode aumentar o número de executores dando a cada executor menos memória (por exemplo, 3 GB) para um paralelismo aumentado. No entanto, é aconselhável sintonizar esta definição para a carga de trabalho do seu alvo.

Existem 16 vcores em D14 v2 VMs.
Para D14 v2, o valor recomendado para num dos executores é (16 vcores x 120%) ~= **19** em cada nó de trabalhador considerando 3GB por executor.

***hive.llap.io.threadpool.size:***   
Este valor especifica o tamanho da piscina de fio para executores. Uma vez que os executores são fixados conforme especificado, será o mesmo que o número de executores por daemon LLAP.    
Para D14 v2, o valor recomendado é **19**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. Determinar o tamanho da cache do daemon LLAP**  
Configuração: ***hive.llap.io.memory.size***

A memória do contentor do daemon LLAP consiste em seguintes componentes;
*  Sala da cabeça
*  Memória de heap usada por executores (Xmx)
*  Cache de memória por daemon (o seu tamanho de memória off-heap, não aplicável quando a cache SSD está ativada)
*  Tamanho dos metadados de cache na memória (aplicável apenas quando a cache SSD estiver ativada)

**Tamanho da sala** da cabeça : Este tamanho indica uma parte da memória off-heap usada para a sobrecarga java VM (metaspace, pilha de fios, estruturas de dados gc, etc.). Geralmente, esta sobrecarga é de cerca de 6% do tamanho da pilha (Xmx). Para estar no lado mais seguro, este valor pode ser calculado como 6% do tamanho total da memória da DEEMON LLAP.  
Para D14 v2, o valor recomendado é ceil (80 GB x 0,06) ~= **4 GB**.  

**Tamanho da pilha (Xmx)**: É uma quantidade de memória de pilha disponível para todos os executores.
Tamanho total da pilha = Número de executores x 3 GB  
Para D14 v2, este valor é de 19 x 3 GB = **57 GB**  

`Ambari environment variable for LLAP heap size:`

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png " alt-text="'LLAP tamanho da pilha'" border="true":::

Quando a cache SSD é desativada, a cache na memória é a quantidade de memória que é deixada depois de retirar o tamanho da sala de cabeceira e o tamanho do heap do tamanho do recipiente do daemon LLAP.

O cálculo do tamanho da cache difere quando a cache SSD está ativada.
Configuração *da colmeia.llap.io.allocator.mmap* = verdadeiro permitirá o caching SSD.
Quando a cache SSD estiver ativada, alguma parte da memória será usada para armazenar metadados para a cache SSD. Os metadados são armazenados na memória e espera-se que sejam ~8% do tamanho da cache SSD.   
Tamanho dos metadados de cache na memória SSD = tamanho do recipiente do daemon LLAP - (Sala da cabeça + tamanho da pilha)  
Para D14 v2, com HDI 4.0, tamanho de metadados de cache SSD na memória = 80 GB - (4 GB + 57 GB) = **19 GB**  
Para D14 v2, com HDI 3.6, tamanho de metadados de cache SSD na memória = 79 GB - (4 GB + 57 GB) = **18 GB**

Dado o tamanho da memória disponível para armazenar metadados de cache SSD, podemos calcular o tamanho da cache SSD que pode ser suportada.  
Tamanho dos metadados na memória para cache SSD = tamanho do contentor doemon LLAP - (Sala da cabeça + tamanho da pilha) = 19 GB     
Tamanho da cache SSD = tamanho dos metadados na memória para cache SSD (19 GB) / 0,08 (8 por cento)  

Para D14 v2 e HDI 4.0, o tamanho recomendado de cache SSD = 19 GB / 0,08 ~= **237 GB**  
Para D14 v2 e HDI 3.6, o tamanho recomendado de cache SSD = 18 GB / 0,08 ~= **225 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. Mapa de ajuste Junte memória**   
Configuração: ***hive.auto.convert.join.noconditionaltask.size***

Certifique-se de que tem *hive.auto.convert.join.noconditionaltask* habilitado para que este parâmetro produza efeitos.
Esta configuração determina o limiar para a seleção de MapJoin pelo otimizador hive que considera a subscrição excessiva da memória de outros executores ter mais espaço para tabelas de haxixe na memória para permitir mais conversões de junção de mapas. Considerando 3GB por executor, este tamanho pode ser sobrestimado a 3GB, mas alguma memória de heap também pode ser usada para classificar tampão, baralhar tampão, etc. pelas outras operações.   
Assim, para D14 v2, com memória de 3 GB por executor, é recomendado definir este valor para **2048 MB**.  

(Nota: Este valor pode necessitar de ajustes adequados à sua carga de trabalho. Definir este valor demasiado baixo pode não utilizar a função de autoconvertida. E defini-lo demasiado alto pode resultar em exceções de memória ou pausas de GC que podem resultar em desempenho adverso.)  

#### <a name="11-number-of-llap-daemons"></a>**11. Número de daemons LLAP**
Variáveis ambientais Ambari: ***num_llap_nodes, num_llap_nodes_for_llap_daemons***  

**num_llap_nodes** - especifica o número de nós usados pelo serviço Hive LLAP, isto inclui nós que executam daemon LLAP, LLAP Service Master e Tez Application Master (Tez AM).  

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png " alt-text="«Número de nós para o serviço LLAP»" border="true":::  

**num_llap_nodes_for_llap_daemons** - número especificado de nós utilizados apenas para os daemons LLAP. Os tamanhos do contentor do daemon LLAP estão ajustados para o nó máximo, de modo que resultará num daemon em cada nó.

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png " alt-text="«Número de nós para os daemons LLAP»" border="true":::

Recomenda-se manter ambos os valores iguais ao número de nós de trabalhadores no cluster de consultas interativas.

### <a name="considerations-for-workload-management"></a>**Considerações para a Gestão da Carga de Trabalho**  
Se pretender ativar a gestão da carga de trabalho para o LLAP, certifique-se de que reserva capacidade suficiente para que a gestão da carga de trabalho funcione como esperado. A gestão da carga de trabalho requer a configuração de uma fila yarn personalizada, que é além da `llap` fila. Certifique-se de que divide a capacidade total de recursos do cluster entre a fila llap e a fila de gestão da carga de trabalho de acordo com os seus requisitos de carga de trabalho.
A gestão da carga de trabalho gera o Tez Application Masters (Tez AMs) quando um plano de recursos é ativado.
Atenção:

* Tez AMs gerada através da ativação de um plano de recursos consome recursos da fila de gestão da carga de trabalho, conforme especificado por `hive.server2.tez.interactive.queue` .  
* O número de Tez AMs dependeria do valor especificado no plano de `QUERY_PARALLELISM` recursos.  
* Uma vez que a gestão da carga de trabalho esteja ativa, tez AMs na fila llap não será usado. Apenas os Tez AMs da fila de gestão da carga de trabalho são utilizados para coordenação de consultas. Os Tez AMs na fila são utilizados quando a `llap` gestão da carga de trabalho é desativada.
 
Por exemplo: Capacidade total do cluster = memória de 100 GB, dividida entre LLAP, Gestão da Carga de Trabalho e Filas Predefinidas da seguinte forma:
 - capacidade de fila llap = 70 GB
 - Capacidade de fila de gestão da carga de trabalho = 20 GB
 - Capacidade de fila padrão = 10 GB

Com 20 GB na capacidade de fila de gestão da carga de trabalho, um plano de recursos pode especificar `QUERY_PARALLELISM` o valor como cinco, o que significa que a gestão da carga de trabalho pode lançar cinco Tez AMs com tamanho de contentor de 4 GB cada. Se `QUERY_PARALLELISM` for superior à capacidade, poderá ver algumas Tez AMs pararem de responder no `ACCEPTED` estado. O Hiveserver2 Interactive não pode submeter fragmentos de consulta às AMs Tez que não estão no `RUNNING` estado.


#### <a name="next-steps"></a>**Passos Seguintes**
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