---
title: Testar a replicação de VM do Hyper-V em um site secundário com o VMM usando Azure Site Recovery
description: Este artigo fornece informações sobre o teste de desempenho para a replicação de VMs do Hyper-V em nuvens do VMM para um site secundário usando Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 3edd182e335bc679d95d7be64f45b617a9f54c1a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663177"
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
| Servidores Hyper-V no cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 tem 256 |Dell™ PowerEdge™ R820 |Intel (R) Xeon (R) CPU E5-4620 0 \@ 2,20 GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + função Hyper-V |
| Servidor do VMM |2 | | |2 |1 Gbps |Banco de dados do Windows Server 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Site secundário

* O site secundário tem um cluster de failover de seis nós.
* O armazenamento para o nó de cluster é fornecido por uma SAN iSCSI. Modelo – Hitachi HUS130.

![Especificação de hardware primário](./media/hyper-v-vmm-performance-results/IC744923.png)

| Servidor | RAM | Modelo | Processador | Número de processadores | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servidores Hyper-V no cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell™ PowerEdge™ R720 |Intel (R) Xeon (R) CPU E5-2630 0 \@ 2,30 GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + função Hyper-V |
| ESTLAB-HOST17 |128 |Dell™ PowerEdge™ R820 |Intel (R) Xeon (R) CPU E5-4620 0 \@ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + função Hyper-V |
| ESTLAB-HOST24 |256 |Dell™ PowerEdge™ R820 |Intel (R) Xeon (R) CPU E5-4620 0 \@ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + função Hyper-V |
| Servidor do VMM |2 | | |2 |1 Gbps |Banco de dados do Windows Server 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Cargas de trabalho do servidor

* Para fins de teste, escolhemos as cargas de trabalho comumente usadas em cenários de clientes empresariais.
* Usamos o [Iometer](http://www.iometer.org) com a característica de carga de trabalho resumida na tabela para simulação.
* Todos os perfis do IOMeter são definidos para gravar bytes aleatórios para simular padrões de gravação de pior caso para cargas de trabalho.

| Carga de trabalho | Tamanho de e/s (KB) | % De acesso | % De leitura | E/SS pendentes | Padrão de e/s |
| --- | --- | --- | --- | --- | --- |
| Servidor de Ficheiros |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Todos os 100% aleatórios |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% aleatório<br />100% sequencial |
| Troca |32 |100% |67% |8 |100% aleatório |
| Estação de trabalho/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Ambos 100% aleatórios |
| Servidor de arquivos da Web |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Todos os 75% aleatórios |

### <a name="vm-configuration"></a>Configuração da VM

* 470 VMs no cluster primário.
* Todas as VMs com disco VHDX.
* VMs que executam cargas de trabalho resumidas na tabela. Todos foram criados com modelos do VMM.

| Carga de trabalho | N.º de VMs | RAM mínima (GB) | RAM máxima (GB) | Tamanho do disco lógico (GB) por VM | IOPS máximo |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Servidor de Ficheiros |50 |1 |2 |552 |22 |
| VDI |149 |5 |1 |80 |6 |
| Servidor Web |149 |5 |1 |80 |6 |
| COMPLETA |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Configurações de Site Recovery

* Site Recovery foi configurado para proteção de local para local
* O servidor do VMM tem quatro nuvens configuradas, contendo os servidores de cluster do Hyper-V e suas VMs.

| Nuvem do VMM primário | VMs protegidas | Frequência de replicação | Pontos de recuperação adicionais |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minutos |Nenhum |
| PrimaryCloudRpo30s |47 |30 segundos |Nenhum |
| PrimaryCloudRpo30sArp1 |47 |30 segundos |1 |
| PrimaryCloudRpo5m |235 |5 mins |Nenhum |

### <a name="performance-metrics"></a>Métricas de desempenho

A tabela resume as métricas de desempenho e os contadores que foram medidos na implantação.

| Métrica | Neutraliza |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Memória disponível |\ Mbytes Mbytes |
| IOPS |\PhysicalDisk (_Total) \Bytes Transfers/s |
| Operações de leitura de VM (IOPS)/s |Dispositivo de armazenamento virtual \Dispositivo (\<VHD >) operações \/s |
| Operações de gravação de VM (IOPS)/s |\Dispositivo do dispositivo de armazenamento virtual (\<VHD >) operações de \/S |
| Taxa de transferência de leitura da VM |Dispositivo de armazenamento virtual \Dispositivo (\<VHD >) \ Bytes/s |
| Taxa de transferência de gravação da VM |Dispositivo de armazenamento virtual \Dispositivo (\<VHD >) \ Bytes/s |

## <a name="next-steps"></a>Passos seguintes

[Configurar a replicação](hyper-v-vmm-disaster-recovery.md)
