---
title: Resultados de teste para replicação de VMs do Hyper-V em nuvens do VMM para um site secundário com Azure Site Recovery | Microsoft Docs
description: Este artigo fornece informações sobre o teste de desempenho para a replicação de VMs do Hyper-V em nuvens do VMM para um site secundário usando Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: a7413b2dcb24a42092eb2af9816b1d29a8306e19
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377220"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Resultados de teste para replicação do Hyper-V para um site secundário


Este artigo fornece os resultados do teste de desempenho ao replicar VMs do Hyper-V em nuvens System Center Virtual Machine Manager (VMM) para um datacenter secundário.

## <a name="test-goals"></a>Metas de teste

O objetivo do teste era examinar como o Site Recovery é executado durante a replicação de estado estável.

- A replicação de estado estável ocorre quando as VMs concluíram a replicação inicial e estão sincronizando alterações delta.
- É importante medir o desempenho usando o estado Steady, pois é o estado no qual a maioria das VMs permanece, a menos que ocorram interrupções inesperadas.
- A implantação de teste consistia em dois sites locais, com um servidor VMM em cada site. Essa implantação de teste é típica de uma implantação de escritório/filial, com a sede atuando como o site primário e a filial como o site secundário ou de recuperação.

## <a name="what-we-did"></a>O que fizemos

Veja o que fizemos na fase de teste:

1. VMs criadas usando modelos do VMM.
2. VMs iniciadas e capturadas métricas de desempenho de linha de base em 12 horas.
3. Foram criadas nuvens nos servidores VMM primário e de recuperação.
4. Replicação configurada no Site Recovery, incluindo o mapeamento entre nuvens de origem e recuperação.
5. Habilitada a proteção para VMs e permitiu que elas concluíssem a replicação inicial.
6. Esperou algumas horas para estabilização do sistema.
7. Métricas de desempenho capturadas em 12 horas, em que todas as VMs permaneceram em um estado de replicação esperado por essas 12 horas.
8. Mediu o Delta entre as métricas de desempenho de linha de base e as métricas de desempenho de replicação.


## <a name="primary-server-performance"></a>Desempenho do servidor primário

* A réplica do Hyper-V (usada pelo Site Recovery) controla de forma assíncrona as alterações em um arquivo de log, com sobrecarga mínima de armazenamento no servidor primário.
* A réplica do Hyper-V utiliza o cache de memória automantido para minimizar a sobrecarga de IOPS para acompanhamento. Ele armazena gravações no VHDX na memória e os libera para o arquivo de log antes da hora em que o log é enviado para o site de recuperação. Uma liberação de disco também ocorre se as gravações atingirem um limite predeterminado.
* O gráfico a seguir mostra a sobrecarga de IOPS de estado estável para replicação. Podemos ver que a sobrecarga de IOPS devido à replicação é de aproximadamente 5%, o que é muito baixo.

  ![Resultados primários](./media/hyper-v-vmm-performance-results/IC744913.png)

A réplica do Hyper-V usa a memória no servidor primário para otimizar o desempenho do disco. Conforme mostrado no grafo a seguir, a sobrecarga de memória em todos os servidores no cluster primário é marginal. A sobrecarga de memória mostrada é a porcentagem de memória usada pela replicação, em comparação com a memória total instalada no servidor Hyper-V.

![Resultados primários](./media/hyper-v-vmm-performance-results/IC744914.png)

A réplica do Hyper-V tem sobrecarga mínima de CPU. Conforme mostrado no grafo, a sobrecarga de replicação está no intervalo de 2-3%.

![Resultados primários](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Desempenho do servidor secundário

A réplica do Hyper-V usa uma pequena quantidade de memória no servidor de recuperação para otimizar o número de operações de armazenamento. O grafo resume o uso de memória no servidor de recuperação. A sobrecarga de memória mostrada é a porcentagem de memória usada pela replicação, em comparação com a memória total instalada no servidor Hyper-V.

![Resultados secundários](./media/hyper-v-vmm-performance-results/IC744916.png)

A quantidade de operações de e/s no site de recuperação é uma função do número de operações de gravação no site primário. Vamos examinar o total de operações de e/s no site de recuperação em comparação com o total de operações de e/s e operações de gravação no site primário. Os gráficos mostram que o IOPS total no site de recuperação é

* Cerca de 1,5 vezes o IOPS de gravação no primário.
* Cerca de 37% do IOPS total no site primário.

![Resultados secundários](./media/hyper-v-vmm-performance-results/IC744917.png)

![Resultados secundários](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Efeito na utilização da rede

Uma média de 275 MB por segundo de largura de banda de rede foi usada entre os nós primário e de recuperação (com compactação habilitada) em relação a uma largura de banda existente de 5 GB por segundo.

![Resultados de utilização de rede](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Efeito no desempenho da VM

Uma consideração importante é o impacto da replicação em cargas de trabalho de produção em execução nas máquinas virtuais. Se o site primário estiver adequadamente provisionado para replicação, não deverá haver nenhum impacto nas cargas de trabalho. O mecanismo de controle leve da réplica do Hyper-V garante que as cargas de trabalho em execução nas máquinas virtuais não sejam afetadas durante a replicação de estado estável. Isso é ilustrado nos gráficos a seguir.

Este grafo mostra o IOPS executado por máquinas virtuais que executam cargas de trabalho diferentes, antes e depois da habilitação da replicação. Você pode observar que não há nenhuma diferença entre os dois.

![Resultados de efeito de réplica](./media/hyper-v-vmm-performance-results/IC744920.png)

O grafo a seguir mostra a taxa de transferência de máquinas virtuais que executam cargas de trabalho diferentes, antes e depois da habilitação da replicação. Você pode observar que a replicação não tem impacto significativo.

![Efeitos de réplica de resultados](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Conclusão

Os resultados mostram claramente que Site Recovery, juntamente com a réplica do Hyper-V, são bem dimensionados com a sobrecarga mínima para um cluster grande. O Site Recovery fornece implantação, replicação, gerenciamento e monitoramento simples. A réplica do Hyper-V fornece a infraestrutura necessária para o dimensionamento bem-sucedido da replicação. 

## <a name="test-environment-details"></a>Detalhes do ambiente de teste

### <a name="primary-site"></a>Site primário

* O site primário tem um cluster que contém cinco servidores Hyper-V, executando máquinas virtuais 470.
* As VMs executam cargas de trabalho diferentes e todas têm a proteção Site Recovery habilitada.
* O armazenamento para o nó de cluster é fornecido por uma SAN iSCSI. Modelo – Hitachi HUS130.
* Cada servidor de cluster tem quatro placas de rede (NICs) de um Gbps cada.
* Duas das placas de rede estão conectadas a uma rede iSCSI privada e duas estão conectadas a uma rede corporativa externa. Uma das redes externas é reservada somente para comunicações de cluster.

![Requisitos de hardware primários](./media/hyper-v-vmm-performance-results/IC744922.png)

| Servidor | RAM | Modelo | Processador | Número de processadores | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servidores Hyper-V no cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 tem 256 |Dell ™ PowerEdge ™ R820 |Intel (r) Xeon (r) CPU E5-4620 0 \@ 2,20 GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + função Hyper-V |
| Servidor do VMM |2 | | |2 |1 Gbps |Banco de dados do Windows Server 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Site secundário

* O site secundário tem um cluster de failover de seis nós.
* O armazenamento para o nó de cluster é fornecido por uma SAN iSCSI. Modelo – Hitachi HUS130.

![Especificação de hardware primário](./media/hyper-v-vmm-performance-results/IC744923.png)

| Servidor | RAM | Modelo | Processador | Número de processadores | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servidores Hyper-V no cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel (r) Xeon (r) CPU E5-2630 0 \@ 2,30 GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + função Hyper-V |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel (r) Xeon (r) CPU E5-4620 0 \@ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + função Hyper-V |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel (r) Xeon (r) CPU E5-4620 0 \@ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + função Hyper-V |
| Servidor do VMM |2 | | |2 |1 Gbps |Banco de dados do Windows Server 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Cargas de trabalho do servidor

* Para fins de teste, escolhemos as cargas de trabalho comumente usadas em cenários de clientes empresariais.
* Usamos o [Iometer](http://www.iometer.org) com a característica de carga de trabalho resumida na tabela para simulação.
* Todos os perfis do IOMeter são definidos para gravar bytes aleatórios para simular padrões de gravação de pior caso para cargas de trabalho.

| Carga de trabalho | Tamanho de e/s (KB) | % De acesso | %Read | E/SS pendentes | Padrão de e/s |
| --- | --- | --- | --- | --- | --- |
| Servidor de Ficheiros |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Todos os 100% aleatórios |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% aleatório<br />100% sequencial |
| Exchange |32 |100% |67% |8 |100% aleatório |
| Estação de trabalho/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Ambos 100% aleatórios |
| Servidor de arquivos da Web |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Todos os 75% aleatórios |

### <a name="vm-configuration"></a>Configuração da VM

* 470 VMs no cluster primário.
* Todas as VMs com disco VHDX.
* VMs que executam cargas de trabalho resumidas na tabela. Todos foram criados com modelos do VMM.

| Carga de trabalho | N º de VMs | RAM mínima (GB) | RAM máxima (GB) | Tamanho do disco lógico (GB) por VM | IOPS máximo |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Servidor de Ficheiros |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Servidor Web |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Configurações de Site Recovery

* Site Recovery foi configurado para proteção de local para local
* O servidor do VMM tem quatro nuvens configuradas, contendo os servidores de cluster do Hyper-V e suas VMs.

| Nuvem do VMM primário | VMs protegidas | Frequência de replicação | Pontos de recuperação adicionais |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minutos |Nenhum |
| PrimaryCloudRpo30s |47 |30 segundos |Nenhum |
| PrimaryCloudRpo30sArp1 |47 |30 segundos |1 |
| PrimaryCloudRpo5m |235 |5 mins |Nenhuma |

### <a name="performance-metrics"></a>Métricas de desempenho

A tabela resume as métricas de desempenho e os contadores que foram medidos na implantação.

| Métrica | Contador |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Available memory |\Memory\Available MBytes |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VM read (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| VM write (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Write Operations/S |
| VM read throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM write throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## Next steps

[Set up replication](hyper-v-vmm-disaster-recovery.md)\Processor(_Total)\` Processor Timeation of Hyper-V VMs in VMM clouds to a secondary site with Azure Site Recovery | Microsoft Docs
description: This article provides information about performance testing for replication of Hyper-V VMs in VMM clouds to a secondary site using Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi

---
# Test results for Hyper-V replication to a secondary site


This article provides the results of performance testing when replicating Hyper-V VMs in System Center Virtual Machine Manager (VMM) clouds, to a secondary datacenter.

## Test goals

The goal of testing was to examine how Site Recovery performs during steady state replication.

- Steady state replication occurs when VMs have completed initial replication, and are synchronizing delta changes.
- It’s important to measure performance using steady state, because it’s the state in which most VMs remain, unless unexpected outages occur.
- The test deployment consisted of two on-premises sites, with a VMM server in each site. This test deployment is typical of a head office/branch office deployment, with head office acting as the primary site, and the branch office as the secondary or recovery site.

## What we did

Here's what we did in the test pass:

1. Created VMs using VMM templates.
2. Started VMs, and captured baseline performance metrics over 12 hours.
3. Created clouds on the primary and recovery VMM servers.
4. Configured replication in Site Recovery, including mapping between source and recovery clouds.
5. Enabled protection for VMs, and allowed them to complete initial replication.
6. Waited a couple of hours for system stabilization.
7. Captured performance metrics over 12 hours, where all VMs remained in an expected replication state for those 12 hours.
8. Measured the delta between the baseline performance metrics, and the replication performance metrics.


## Primary server performance

* Hyper-V Replica (used by Site Recovery) asynchronously tracks changes to a log file, with minimum storage overhead on the primary server.
* Hyper-V Replica utilizes self-maintained memory cache to minimize IOPS overhead for tracking. It stores writes to the VHDX in memory, and flushes them into the log file before the time that the log is sent to the recovery site. A disk flush also happens if the writes hit a predetermined limit.
* The graph below shows the steady state IOPS overhead for replication. We can see that the IOPS overhead due to replication is around 5%, which is quite low.

  ![Primary results](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V Replica uses memory on the primary server, to optimize disk performance. As shown in the following graph, memory overhead on all servers in the primary cluster is marginal. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Primary results](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V Replica has minimum CPU overhead. As shown in the graph, replication overhead is in the range of 2-3%.

![Primary results](./media/hyper-v-vmm-performance-results/IC744915.png)

## Secondary server performance

Hyper-V Replica uses a small amount of memory on the recovery server, to optimize the number of storage operations. The graph summarizes the memory usage on the recovery server. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744916.png)

The amount of I/O operations on the recovery site is a function of the number of write operations on the primary site. Let’s look at the total I/O operations on the recovery site in comparison with the total I/O operations and write operations on the primary site. The graphs show that the total IOPS on the recovery site is

* Around 1.5 times the write IOPS on the primary.
* Around 37% of the total IOPS on the primary site.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744917.png)

![Secondary results](./media/hyper-v-vmm-performance-results/IC744918.png)

## Effect on network utilization

An average of 275 Mb per second of network bandwidth was used between the primary and recovery nodes (with compression enabled), against an existing bandwidth of 5 Gb per second.

![Results network utilization](./media/hyper-v-vmm-performance-results/IC744919.png)

## Effect on VM performance

An important consideration is the impact of replication on production workloads running on the virtual machines. If the primary site is adequately provisioned for replication, there shouldn’t be any impact on the workloads. Hyper-V Replica’s lightweight tracking mechanism ensures that workloads running in the virtual machines are not impacted during steady-state replication. This is illustrated in the following graphs.

This graph shows IOPS performed by virtual machines running different workloads, before and after replication was enabled. You can observe that there is no difference between the two.

![Replica effect results](./media/hyper-v-vmm-performance-results/IC744920.png)

The following graph shows the throughput of virtual machines running different workloads, before and after replication was enabled. You can observe that replication has no significant impact.

![Results replica effects](./media/hyper-v-vmm-performance-results/IC744921.png)

## Conclusion

The results clearly show that Site Recovery, coupled with Hyper-V Replica, scales well with minimum overhead for a large cluster. Site Recovery provides simple deployment, replication, management and monitoring. Hyper-V Replica provides the necessary infrastructure for successful replication scaling. 

## Test environment details

### Primary site

* The primary site has a cluster containing five Hyper-V servers, running 470 virtual machines.
* The VMs run different workloads, and all have Site Recovery protection enabled.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.
* Each cluster server has four network cards (NICs) of one Gbps each.
* Two of the network cards are connected to an iSCSI private network, and two are connected to an external enterprise network. One of the external networks is reserved for cluster communications only.

![Primary hardware requirements](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 has 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Secondary site

* The secondary site has a six-node failover cluster.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.

![Primary hardware specification](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Server workloads

* For test purposes we picked workloads commonly used in enterprise customer scenarios.
* We use [IOMeter](http://www.iometer.org) with the workload characteristic summarized in the table for simulation.
* All IOMeter profiles are set to write random bytes to simulate worst-case write patterns for workloads.

| Workload | I/O size (KB) | % Access | %Read | Outstanding I/Os | I/O pattern |
| --- | --- | --- | --- | --- | --- |
| File Server |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |All 100% random |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% random<br />100% sequential |
| Exchange |32 |100% |67% |8 |100% random |
| Workstation/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Both 100% random |
| Web File Server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |All 75% random |

### VM configuration

* 470 VMs on the primary cluster.
* All VMs with VHDX disk.
* VMs running workloads summarized in the table. All were created with VMM templates.

| Workload | # VMs | Minimum RAM (GB) | Maximum RAM (GB) | Logical disk size (GB) per VM | Maximum IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| File Server |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Web server |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96.83 TB |4108 |

### Site Recovery settings

* Site Recovery was configured for on-premises to on-premises protection
* The VMM server has four clouds configured, containing the Hyper-V cluster servers and their VMs.

| Primary VMM cloud | Protected VMs | Replication frequency | Additional recovery points |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 mins |None |
| PrimaryCloudRpo30s |47 |30 secs |None |
| PrimaryCloudRpo30sArp1 |47 |30 secs |1 |
| PrimaryCloudRpo5m |235 |5 mins |None |

### Performance metrics

The table summarizes the performance metrics and counters that were measured in the deployment.

| Metric | Counter |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Memória disponível |\ Mbytes Mbytes |
| IOPS |\PhysicalDisk (_ total) \Bytes transferências de segundos/s |
| Operações de leitura de VM (IOPS)/s |\Dispositivo do dispositivo de armazenamento\<virtual (VHD >) \ operações/s |
| Operações de gravação de VM (IOPS)/s |\Dispositivo do dispositivo de armazenamento\<virtual (VHD >) \ operações/S |
| Taxa de transferência de leitura da VM |Dispositivo de armazenamento virtual \dispositivo\<(VHD >) \ Bytes/s |
| Taxa de transferência de gravação da VM |Dispositivo de armazenamento virtual \dispositivo\<(VHD >) \ Bytes/s |

## <a name="next-steps"></a>Passos Seguintes

[Configurar a replicação](hyper-v-vmm-disaster-recovery.md)
