---
title: Teste a replicação de VM hiper-V para um local secundário com VMM usando a recuperação do site Azure
description: Este artigo fornece informações sobre testes de desempenho para replicação de VMs Hiper-V em nuvens VMM para um site secundário usando a Recuperação do Site Azure.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 3edd182e335bc679d95d7be64f45b617a9f54c1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73663177"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Resultados dos testes para a replicação hyper-V para um local secundário


Este artigo fornece os resultados dos testes de desempenho ao replicar VMs Hiper-V em nuvens de Gestor de Máquinas Virtuais (VMM) do System Center, para um centro de dados secundário.

## <a name="test-goals"></a>Objetivos de teste

O objetivo dos testes era examinar como a Recuperação do Site funciona durante a replicação do estado estável.

- A replicação do estado constante ocorre quando os VMs completaram a replicação inicial, e estão a sincronizar alterações delta.
- É importante medir o desempenho usando um estado estável, porque é o estado em que a maioria dos VMs permanecem, a menos que ocorram interrupções inesperadas.
- A implementação do teste consistia em dois sites no local, com um servidor VMM em cada site. Esta implantação de teste é típica de uma implantação de sede/sede, com a sede a agir como o local principal, e a sucursal como o local secundário ou de recuperação.

## <a name="what-we-did"></a>O que fizemos

Eis o que fizemos no teste:

1. VMs criados usando modelos VMM.
2. Começou os VMs e capturou métricas de desempenho de base ao longo de 12 horas.
3. Criou nuvens nos servidores VMM primários e de recuperação.
4. Replicação configurada na Recuperação do Local, incluindo mapeamento entre nuvens de origem e recuperação.
5. Proteção ativada para VMs, e permitiu-lhes completar a replicação inicial.
6. Esperei algumas horas pela estabilização do sistema.
7. Mediram métricas de desempenho capturadas ao longo de 12 horas, onde todos os VMs permaneceram num estado de replicação esperado durante essas 12 horas.
8. Mediu o delta entre as métricas de desempenho da linha de base e as métricas de desempenho da replicação.


## <a name="primary-server-performance"></a>Desempenho do servidor primário

* A réplica hiper-V (utilizada pela Recuperação do Site) rastreia assincronicamente alterações num ficheiro de registo, com a sobrecarga mínima de armazenamento no servidor primário.
* A Hyper-V Replica utiliza cache de memória auto-mantida para minimizar as despesas gerais do IOPS para o rastreio. Armazena para o VHDX na memória e coloca-as no ficheiro de registo antes do momento em que o registo é enviado para o local de recuperação. Um flush de disco também acontece se as escritas atingirem um limite pré-determinado.
* O gráfico abaixo mostra o estado constante iOPS sobrecarga para replicação. Podemos ver que a sobrecarga do IOPS devido à replicação é de cerca de 5%, o que é bastante baixo.

  ![Resultados primários](./media/hyper-v-vmm-performance-results/IC744913.png)

A Hyper-V Replica utiliza a memória no servidor primário, para otimizar o desempenho do disco. Como mostra o gráfico seguinte, a sobrecarga da memória em todos os servidores do cluster primário é marginal. A sobrecarga da memória mostrada é a percentagem de memória utilizada por replicação, em comparação com a memória total instalada no servidor Hyper-V.

![Resultados primários](./media/hyper-v-vmm-performance-results/IC744914.png)

A Réplica Hyper-V tem uma sobrecarga mínima de CPU. Como mostrado no gráfico, a sobrecarga de replicação está entre 2-3%.

![Resultados primários](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Desempenho do servidor secundário

A Hyper-V Replica utiliza uma pequena quantidade de memória no servidor de recuperação, para otimizar o número de operações de armazenamento. O gráfico resume o uso da memória no servidor de recuperação. A sobrecarga da memória mostrada é a percentagem de memória utilizada por replicação, em comparação com a memória total instalada no servidor Hyper-V.

![Resultados secundários](./media/hyper-v-vmm-performance-results/IC744916.png)

A quantidade de operações de E/S no local de recuperação é uma função do número de operações de escrita no local primário. Vamos ver as operações totais de E/S no local de recuperação em comparação com o total de operações de I/S e as operações de escrita no local principal. Os gráficos mostram que o total de IOPS no local de recuperação é

* Cerca de 1,5 vezes o IOPS escrito na primária.
* Cerca de 37% do total de IOPS no local primário.

![Resultados secundários](./media/hyper-v-vmm-performance-results/IC744917.png)

![Resultados secundários](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Efeito na utilização da rede

Uma média de 275 Mb por segundo de largura de banda de rede foi utilizada entre os nós primários e de recuperação (com compressão ativado), contra uma largura de banda existente de 5 Gb por segundo.

![Utilização da rede de resultados](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Efeito no desempenho da VM

Uma consideração importante é o impacto da replicação nas cargas de trabalho de produção em funcionamento nas máquinas virtuais. Se o local primário estiver adequadamente previsto para a replicação, não deve haver qualquer impacto nas cargas de trabalho. O mecanismo de rastreio leve da Hyper-V Replica garante que as cargas de trabalho em funcionamento nas máquinas virtuais não sejam afetadas durante a replicação do estado estável. Isto é ilustrado nos seguintes gráficos.

Este gráfico mostra iOPS realizado por máquinas virtuais que executam diferentes cargas de trabalho, antes e depois da replicação ter sido ativada. Pode observar que não há diferença entre os dois.

![Resultados do efeito de réplica](./media/hyper-v-vmm-performance-results/IC744920.png)

O gráfico seguinte mostra a entrada de máquinas virtuais que executam diferentes cargas de trabalho, antes e depois da replicação. Pode observar que a replicação não tem impacto significativo.

![Efeitos de réplica de resultados](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Conclusão

Os resultados mostram claramente que a Recuperação do Site, juntamente com a Réplica Hyper-V, escala bem com a sobrecarga mínima para um grande cluster. A Recuperação do Site proporciona uma simples implantação, replicação, gestão e monitorização. A Réplica Hyper-V fornece a infraestrutura necessária para a escala de replicação bem sucedida. 

## <a name="test-environment-details"></a>Testar detalhes ambientais

### <a name="primary-site"></a>Site primário

* O site principal tem um cluster contendo cinco servidores Hyper-V, executando 470 máquinas virtuais.
* Os VMs executam diferentes cargas de trabalho, e todos têm proteção de recuperação do site ativada.
* O armazenamento do nó de cluster é fornecido por um iSCSI SAN. Modelo – Hitachi HUS130.
* Cada servidor de cluster tem quatro cartões de rede (NICs) de um Gbps cada.
* Dois dos cartões de rede estão ligados a uma rede privada iSCSI, e dois estão ligados a uma rede empresarial externa. Uma das redes externas está reservada apenas para comunicações de cluster.

![Requisitos primários de hardware](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Modelo | Processador | Número de processadores | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servidores hiper-V em cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 tem 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Função Hyper-V |
| Servidor VMM |2 | | |2 |1 Gbps |Base de Dados do Servidor do Windows 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Site Secundário

* O local secundário tem um cluster de seis nós.
* O armazenamento do nó de cluster é fornecido por um iSCSI SAN. Modelo – Hitachi HUS130.

![Especificação primária de hardware](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Modelo | Processador | Número de processadores | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servidores hiper-V em cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Função Hyper-V |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Função Hyper-V |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Função Hyper-V |
| Servidor VMM |2 | | |2 |1 Gbps |Base de Dados do Servidor do Windows 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Cargas de trabalho do servidor

* Para efeitos de teste, escolhemos cargas de trabalho comumente usadas em cenários de clientes empresariais.
* Utilizamos [iOMeter](http://www.iometer.org) com a característica da carga de trabalho resumida na tabela para simulação.
* Todos os perfis do IOMeter estão definidos para escrever bytes aleatórios para simular os piores padrões de escrita para cargas de trabalho.

| Carga de trabalho | Tamanho I/O (KB) | % Acesso | %Ler | Excelente I/Os | Padrão De I/O |
| --- | --- | --- | --- | --- | --- |
| Servidor de Ficheiros |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Todos 100% aleatórios |
| Servidor SQL (volume 1)<br />Servidor SQL (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% aleatório<br />100% sequencial |
| Troca |32 |100% |67% |8 |100% aleatório |
| Posto de trabalho/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Ambos 100% aleatórios |
| Servidor de Ficheiros Web |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Todos 75% aleatórios |

### <a name="vm-configuration"></a>Configuração VM

* 470 VMs no aglomerado primário.
* Todos os VMs com disco VHDX.
* VMs executando cargas de trabalho resumidas na mesa. Todos foram criados com modelos VMM.

| Carga de trabalho | VMs | RAM mínimo (GB) | RAM máximo (GB) | Tamanho lógico do disco (GB) por VM | IOPS máximos |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Servidor de Ficheiros |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Servidor Web |149 |.5 |1 |80 |6 |
| Total |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Definições de recuperação do site

* A recuperação do local foi configurada para a proteção no local para o local
* O servidor VMM tem quatro nuvens configuradas, contendo os servidores de cluster Hyper-V e os seus VMs.

| Nuvem VMM primária | VMs protegidas | Frequência de replicação | Pontos de recuperação adicionais |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minutos |Nenhuma |
| PrimaryCloudRpo30s |47 |30 segundos |Nenhuma |
| PrimaryCloudRpo30sArp1 |47 |30 segundos |1 |
| PrimaryCloudRpo5m |235 |5 mins |Nenhuma |

### <a name="performance-metrics"></a>Métricas de desempenho

A tabela resume as métricas de desempenho e os contadores que foram medidos na implantação.

| Métrica | Contador |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Memória disponível |\Memory\MBytes disponíveis |
| IOPS |\PhysicalDisk(_Total)\Transferências de disco/seg |
| VM leitura (IOPS) operações/seg |\Hiper-V Dispositivo de\<Armazenamento Virtual(VHD>)\Ler Operações/Sec |
| VM escrever operações (IOPS) operações/seg |\Hiper-V Dispositivo de\<Armazenamento Virtual (VHD>)\Write Operations/S |
| VM ler a entrada |\Hiper-V Dispositivo de\<Armazenamento Virtual (VHD>)\Read Bytes/seg |
| VM escrever a entrada |\Hiper-V Dispositivo de\<Armazenamento Virtual (VHD>)\Write Bytes/seg |

## <a name="next-steps"></a>Passos seguintes

[Configurar a replicação](hyper-v-vmm-disaster-recovery.md)
