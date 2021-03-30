---
title: Testar a replicação do Hyper-V VM para um local secundário com VMM usando a recuperação do local de Azure
description: Este artigo fornece informações sobre testes de desempenho para replicação de VMs Hiper-V em nuvens VMM para um site secundário usando Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 6c8219214e7053dcf6b119f6cd5dc97daaa355f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92327642"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Resultados do teste para a replicação do Hiper-V num local secundário


Este artigo fornece os resultados dos testes de desempenho ao replicar VMs hiper-V nas nuvens do System Center Virtual Machine Manager (VMM) para um centro de dados secundário.

## <a name="test-goals"></a>Objetivos de teste

O objetivo dos testes era examinar como a Recuperação do Local funciona durante a replicação constante do estado.

- A replicação constante do estado ocorre quando os VMs completam a replicação inicial, e estão a sincronizar alterações delta.
- É importante medir o desempenho usando o estado estável, porque é o estado em que a maioria dos VMs permanecem, a menos que ocorram interrupções inesperadas.
- A implementação do teste consistia em dois locais no local, com um servidor VMM em cada site. Esta implantação de teste é típica de uma implantação de sede/filial, com sede a funcionar como o local principal, e a sucursal como local secundário ou de recuperação.

## <a name="what-we-did"></a>O que fizemos

Eis o que fizemos no teste:

1. Vims criados usando modelos VMM.
2. Começou vMs, e capturou métricas de desempenho de base em 12 horas.
3. Criou nuvens nos servidores VMM primários e de recuperação.
4. Replicação configurada na Recuperação do Local, incluindo mapeamento entre as nuvens de origem e recuperação.
5. Permitiu a proteção dos VMs e permitiu-lhes completar a replicação inicial.
6. Esperei algumas horas pela estabilização do sistema.
7. Capturou métricas de desempenho ao longo de 12 horas, onde todos os VMs permaneceram em estado de replicação esperado durante essas 12 horas.
8. Mediu o delta entre as métricas de desempenho da linha de base e as métricas de desempenho da replicação.


## <a name="primary-server-performance"></a>Desempenho do servidor primário

* A réplica hiper-V (utilizada pela Recuperação do Site) rastreia assíncroneamente as alterações num ficheiro de registo, com a sobrecarga mínima de armazenamento no servidor primário.
* A Réplica Hyper-V utiliza cache de memória auto-conservada para minimizar a sobrecarga do IOPS para rastreio. Armazena a escrita para o VHDX na memória e coloca-os no ficheiro de registo antes do momento em que o registo é enviado para o local de recuperação. Um autoclismo de disco também acontece se as escritas atingirem um limite pré-determinado.
* O gráfico abaixo mostra a sobrecarga de IOPS do estado estável para replicação. Podemos ver que a sobrecarga do IOPS devido à replicação é de cerca de 5%, o que é bastante baixo.

  ![Gráfico que mostra a sobrecarga de IOPS do estado estável para replicação.](./media/hyper-v-vmm-performance-results/IC744913.png)

A réplica hyper-V utiliza a memória no servidor primário para otimizar o desempenho do disco. Como mostrado no gráfico seguinte, a sobrecarga de memória em todos os servidores do cluster primário é marginal. A sobrecarga de memória mostrada é a percentagem de memória utilizada por replicação, em comparação com a memória instalada total no servidor Hiper-V.

![Resultados primários](./media/hyper-v-vmm-performance-results/IC744914.png)

A réplica hiper-V tem a sobrecarga mínima de CPU. Como mostrado no gráfico, a sobrecarga de replicação está na gama de 2-3%.

![Gráfico que mostra a sobrecarga de replicação está na faixa de 2-3%.](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Desempenho do servidor secundário

A Réplica Hiper-V utiliza uma pequena quantidade de memória no servidor de recuperação, para otimizar o número de operações de armazenamento. O gráfico resume o uso da memória no servidor de recuperação. A sobrecarga de memória mostrada é a percentagem de memória utilizada por replicação, em comparação com a memória instalada total no servidor Hiper-V.

![Gráfico que resume o uso da memória no servidor de recuperação.](./media/hyper-v-vmm-performance-results/IC744916.png)

A quantidade de operações de E/S no local de recuperação é uma função do número de operações de escrita no site primário. Vamos olhar para o total de operações de E/S no local de recuperação em comparação com o total de operações de E/S e escrever operações no site principal. Os gráficos mostram que o total de IOPS no local de recuperação é

* Cerca de 1,5 vezes o IOPS de escrita nas primárias.
* Cerca de 37% do total de IOPS no local primário.

![Gráfico que mostra uma comparação de PIOS em sites primários e secundários.](./media/hyper-v-vmm-performance-results/IC744917.png)

![Resultados secundários](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Efeito na utilização da rede

Uma média de 275 Mb por segundo de largura de banda de rede foi usada entre os nós primários e de recuperação (com compressão ativada), contra uma largura de banda existente de 5 Gb por segundo.

![Utilização da rede de resultados](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Efeito no desempenho do VM

Uma consideração importante é o impacto da replicação nas cargas de trabalho de produção que estão a decorrer nas máquinas virtuais. Se o local primário for adequadamente previsto para a replicação, não deve haver qualquer impacto nas cargas de trabalho. O mecanismo de rastreio leve da Réplica Hyper-V garante que as cargas de trabalho em funcionamento nas máquinas virtuais não sejam impactadas durante a replicação do estado constante. Isto é ilustrado nos seguintes gráficos.

Este gráfico mostra iOPS realizado por máquinas virtuais executando diferentes cargas de trabalho, antes e depois de a replicação ter sido ativada. Pode observar que não há diferença entre os dois.

![Resultados do efeito de réplica](./media/hyper-v-vmm-performance-results/IC744920.png)

O gráfico seguinte mostra a produção de máquinas virtuais que executam diferentes cargas de trabalho, antes e depois da replicação ter sido ativada. Pode observar que a replicação não tem impacto significativo.

![Efeitos de réplica de resultados](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Conclusão

Os resultados mostram claramente que a Recuperação do Site, juntamente com a Réplica Hiper-V, escala bem com a sobrecarga mínima para um grande cluster. A Recuperação do Site proporciona uma simples implantação, replicação, gestão e monitorização. A Réplica Hyper-V fornece a infraestrutura necessária para uma escala de replicação bem sucedida. 

## <a name="test-environment-details"></a>Detalhes do ambiente de teste

### <a name="primary-site"></a>Site primário

* O site principal tem um cluster contendo cinco servidores Hiper-V, executando 470 máquinas virtuais.
* Os VMs executam diferentes cargas de trabalho, e todos têm proteção de recuperação do local ativada.
* O armazenamento do nó de cluster é fornecido por um iSCSI SAN. Modelo – Hitachi HUS130.
* Cada servidor de cluster tem quatro cartões de rede (NICs) de um Gbps cada.
* Dois dos cartões de rede estão ligados a uma rede privada iSCSI e dois estão ligados a uma rede de empresas externas. Uma das redes externas está reservada apenas para comunicações de cluster.

![Requisitos de hardware primários](./media/hyper-v-vmm-performance-results/IC744922.png)

| Servidor | RAM | Modelação | Processador | Número de processadores | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servidores hiper-V em cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 tem 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |I Gbps x 4 |Centro de dados do Windows Server 2012 R2 (x64) + Função Hyper-V |
| Servidor VMM |2 | | |2 |1 Gbps |Base de Dados do Servidor do Windows 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Site Secundário

* O local secundário tem um aglomerado de falha de seis nós.
* O armazenamento do nó de cluster é fornecido por um iSCSI SAN. Modelo – Hitachi HUS130.

![Especificação de hardware primário](./media/hyper-v-vmm-performance-results/IC744923.png)

| Servidor | RAM | Modelação | Processador | Número de processadores | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servidores hiper-V em cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Centro de dados do Windows Server 2012 R2 (x64) + Função Hyper-V |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Centro de dados do Windows Server 2012 R2 (x64) + Função Hyper-V |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Centro de dados do Windows Server 2012 R2 (x64) + Função Hyper-V |
| Servidor VMM |2 | | |2 |1 Gbps |Base de Dados do Servidor do Windows 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Cargas de trabalho do servidor

* Para fins de teste, escolhemos cargas de trabalho comumente utilizadas em cenários de clientes da empresa.
* Utilizamos [o IOMeter](http://www.iometer.org) com a característica da carga de trabalho resumida na tabela para simulação.
* Todos os perfis IOMeter são definidos para escrever bytes aleatórios para simular padrões de escrita na pior das hipóteses para cargas de trabalho.

| Carga de trabalho | Tamanho I/O (KB) | % Acesso | %Ler | I/Os pendentes | Padrão de I/O |
| --- | --- | --- | --- | --- | --- |
| Servidor de Ficheiros |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Todos 100% aleatórios |
| SqL Server (volume 1)<br />SqL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% aleatório<br />100% sequencial |
| Troca |32 |100% |67% |8 |100% aleatório |
| Estação de trabalho/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Ambos 100% aleatórios |
| Servidor de ficheiros web |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Todos 75% aleatórios |

### <a name="vm-configuration"></a>Configuração VM

* 470 VMs no aglomerado primário.
* Todos os VMs com disco VHDX.
* VMs executando cargas de trabalho resumidas na tabela. Todos foram criados com modelos VMM.

| Carga de trabalho | VMs | RAM mínimo (GB) | RAM máximo (GB) | Tamanho do disco lógico (GB) por VM | IOPS máximos |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Servidor de Ficheiros |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Servidor Web |149 |.5 |1 |80 |6 |
| Total |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Definições de recuperação de site

* A recuperação do local foi configurada para proteção no local para proteção no local
* O servidor VMM tem quatro nuvens configuradas, contendo os servidores de cluster Hyper-V e os seus VMs.

| Nuvem de VMM primária | VMs protegidas | Frequência de replicação | Pontos de recuperação adicionais |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minutos |Nenhum |
| PrimaryCloudRpo30s |47 |30 segundos |Nenhum |
| PrimaryCloudRpo30sArp1 |47 |30 segundos |1 |
| PrimaryCloudRpo5m |235 |5 mins |Nenhum |

### <a name="performance-metrics"></a>Métricas de desempenho

A tabela resume as métricas de desempenho e os contadores que foram medidos na implantação.

| Metric | Contador |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Memória disponível |\Memory\MBytes disponíveis |
| IOPS |\PhysicalDisk (_Total)\Transferências de disco/seg |
| VM ler operações /seg |\Dispositivo de armazenamento virtual hiper-V. \<VHD> |
| VM escrever operações /seg |\Dispositivo de armazenamento virtual hiper-V. \<VHD> |
| VM ler produção |\Dispositivo de armazenamento virtual Hiper-V. \<VHD> |
| VM escrever produção |\Dispositivo de armazenamento virtual Hiper-V. \<VHD> |

## <a name="next-steps"></a>Passos seguintes

[Configurar a replicação](hyper-v-vmm-disaster-recovery.md)
