---
title: Resultados do teste para a replicação de VMs de Hyper-V em clouds do VMM para um site secundário com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo fornece informações sobre testes de desempenho para a replicação de VMs de Hyper-V em clouds do VMM para um site secundário com o Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 7e2f5c344a0fb632956ab5d5b951ee69cff528ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60363602"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Resultados do teste para replicação de Hyper-V para um site secundário


Este artigo fornece os resultados de teste ao replicar VMs de Hyper-V em clouds do System Center Virtual Machine Manager (VMM) para um datacenter secundário de desempenho.

## <a name="test-goals"></a>Objetivos de teste

O objetivo do teste era examinar a forma como o Site Recovery executa durante a replicação de estado estável.

- Replicação de estado estável ocorre quando as VMs concluíram a replicação inicial e estiver a sincronizar alterações delta.
- É importante medir o desempenho com o estado de repouso, porque é o estado em que a maioria das VMs permanecem, a menos que ocorrerem falhas inesperadas.
- A implementação de teste consistiu em dois sites no local, com um servidor VMM em cada site. Esta implementação de teste é típica de uma implantação do office de sucursais/escritórios principal, com sede que atua como o site primário e o escritório da filial que o site secundário ou de recuperação.

## <a name="what-we-did"></a>O que fizemos

Eis o que no teste passamos:

1. VMs criadas com modelos do VMM.
2. VMs a utilizar e capturado métricas de desempenho de linha de base mais de 12 horas.
3. Nuvens criadas no principal e servidores do VMM de recuperação.
4. Replicação configurada no Site Recovery, incluindo o mapeamento entre a origem e de recuperação de clouds.
5. Ativou a proteção para VMs e permissão-los para concluir a replicação inicial.
6. Aguardado algumas horas para a estabilização do sistema.
7. Capturar métricas de desempenho superior a 12 horas, em que todas as VMs permaneceram num Estado de replicação esperado para essas 12 horas.
8. Medido o delta entre as métricas de desempenho de linha de base e as métricas de desempenho de replicação.


## <a name="primary-server-performance"></a>Desempenho do servidor primário

* Réplica Hyper-V (utilizado pelo Site Recovery) assincronamente controla as alterações para um ficheiro de registo, com o armazenamento mínimo de sobrecarga no servidor primário.
* Réplica do Hyper-V utiliza a cache de memória de Self-manter para minimizar o IOPS sobrecarga para o controlo. Ele armazena escreve o VHDX na memória e os descarrega no ficheiro de registo antes do tempo que o registo é enviado para o site de recuperação. Um descarregamento de disco também acontece se as gravações atingir um limite predeterminado.
* O gráfico abaixo mostra a sobrecarga IOPS de estado estável para replicação. Podemos ver que o IOPS sobrecarga devido a replicação é aproximadamente 5%, que é bastante baixa.

  ![Resultados principais](./media/hyper-v-vmm-performance-results/IC744913.png)

Réplica do Hyper-V utiliza a memória no servidor primário, para otimizar o desempenho de disco. Como mostra o gráfico seguinte, a memória sobrecarga em todos os servidores do cluster principal é marginal. A memória sobrecarga mostrada é a percentagem de memória utilizada pela replicação, em comparação comparada o total de memória instalada no servidor de Hyper-V.

![Resultados principais](./media/hyper-v-vmm-performance-results/IC744914.png)

Réplica do Hyper-V tem a sobrecarga da CPU mínima. Conforme mostrado no gráfico, sobrecarga de replicação está no intervalo de % 2 e 3.

![Resultados principais](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Desempenho do servidor secundário

Réplica do Hyper-V utiliza uma pequena quantidade de memória no servidor de recuperação, para otimizar o número de operações de armazenamento. O gráfico resume a utilização da memória no servidor de recuperação. A memória sobrecarga mostrada é a percentagem de memória utilizada pela replicação, em comparação comparada o total de memória instalada no servidor de Hyper-V.

![Resultados secundários](./media/hyper-v-vmm-performance-results/IC744916.png)

A quantidade de operações de e/s no site de recuperação é uma função do número de operações de escrita no site primário. Vamos examinar as operações de e/s total no site de recuperação em comparação com as operações de e/s total e escrita no site primário. Os gráficos mostram que o total de IOPS no site de recuperação é

* Cerca de 1,5 vezes o IOPS de escrita principal.
* Cerca de 37% do IOPS total no site primário.

![Resultados secundários](./media/hyper-v-vmm-performance-results/IC744917.png)

![Resultados secundários](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Efeito na utilização da rede

Uma média de 275 Mb por segundo de largura de banda de rede foi utilizada entre os nós de principal e de recuperação (com a compressão ativada), em relação a uma largura de banda existente de 5 Gb por segundo.

![Utilização da rede de resultados](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Efeito no desempenho da VM

Uma consideração importante é o impacto da replicação em cargas de trabalho de produção em execução nas máquinas virtuais. Se o site primário é aprovisionado adequadamente para a replicação, não deve haver nenhum impacto nas cargas de trabalho. Mecanismo de controlo de lightweight de réplica de Hyper-V assegura que as cargas de trabalho em execução nas máquinas virtuais não são afetadas durante a replicação de estado estável. Isso é ilustrado nos gráficos seguintes.

Este gráfico mostra o que IOPS realizadas por máquinas virtuais em execução diferentes cargas de trabalho, antes e depois da replicação foi ativada. Pode observar que não existe nenhuma diferença entre os dois.

![Resultados do efeito de réplica](./media/hyper-v-vmm-performance-results/IC744920.png)

O gráfico seguinte mostra a taxa de transferência de máquinas virtuais em execução diferentes cargas de trabalho, antes e depois da replicação foi ativada. Pode observar que a replicação não tem qualquer impacto significativo.

![Efeitos de réplica de resultados](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Conclusão

Os resultados mostram claramente que o Site Recovery, juntamente com a réplica do Hyper-V, dimensiona bem com o mínimo de sobrecarga de um cluster de grandes dimensões. Recuperação de sites fornece implementação simples, a replicação, a gestão e monitorização. Réplica do Hyper-V fornece a infraestrutura necessária para dimensionar de replicação com êxito. 

## <a name="test-environment-details"></a>Detalhes do ambiente de teste

### <a name="primary-site"></a>Site primário

* O site primário tem um cluster de cinco servidores de Hyper-V, 470 máquinas de virtuais em execução.
* As VMs executam diferentes cargas de trabalho e todos têm a proteção de recuperação de Site ativada.
* Armazenamento para o nó de cluster é fornecido por uma SAN de iSCSI. Modelo – Hitachi HUS130.
* Cada servidor de cluster tem quatro placas de rede (NICs) de um Gbps.
* Duas das placas de rede estão ligadas a uma rede privada de iSCSI e dois estão ligada a uma rede empresarial externa. Uma das redes externas está reservada para comunicações de cluster apenas.

![Requisitos de hardware principal](./media/hyper-v-vmm-performance-results/IC744922.png)

| Servidor | RAM | Modelo | Processador | Número de processadores | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servidores de Hyper-V num cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128ESTLAB HOST25 tem 256 |Dell ™ PowerEdge ™ R820 |Intel (r) CPUs CPU E5-4620 0 \@ 2.20 GHz |4 |Eu Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + a função Hyper-V |
| Servidor do VMM |2 | | |2 |1 Gbps |Windows Server R2 de 2012 da base de dados (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Site secundário

* O site secundário tem um cluster de ativação pós-falha de seis nós.
* Armazenamento para o nó de cluster é fornecido por uma SAN de iSCSI. Modelo – Hitachi HUS130.

![Especificação de hardware principal](./media/hyper-v-vmm-performance-results/IC744923.png)

| Servidor | RAM | Modelo | Processador | Número de processadores | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servidores de Hyper-V num cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel (r) CPUs CPU E5-2630 0 \@ 2.30 GHz |2 |Eu Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + a função Hyper-V |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel (r) CPUs CPU E5-4620 0 \@ 2.20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + a função Hyper-V |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel (r) CPUs CPU E5-4620 0 \@ 2.20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + a função Hyper-V |
| Servidor do VMM |2 | | |2 |1 Gbps |Windows Server R2 de 2012 da base de dados (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Cargas de trabalho do servidor

* Para fins de teste escolhemos cargas de trabalho geralmente utilizadas em cenários de clientes do enterprise.
* Usamos [IOMeter](http://www.iometer.org) com características de carga de trabalho resumidas na tabela da simulação.
* Todos os perfis de IOMeter estão definidos para escrever padrões para cargas de trabalho de escrita de bytes aleatórios para simular a pior.

| Carga de trabalho | Tamanho de e/s (KB) | % De acesso | %Read | E/s pendentes | Padrão de e/s |
| --- | --- | --- | --- | --- | --- |
| Servidor de Ficheiros |48163264 |60%20%5%5%10% |80%80%80%80%80% |88888 |Todos os 100% aleatório |
| SQL Server (volume 1) do SQL Server (volume 2) |864 |100%100% |70%0% |88 |% de 100% random100 sequencial |
| Troca |32 |100% |67% |8 |100% aleatório |
| Estação de trabalho/VDI |464 |66%34% |70%95% |11 |Ambos os 100% aleatório |
| Servidor de ficheiros do Web |4864 |33%34%33% |95%95%95% |888 |Todos os 75% aleatório |

### <a name="vm-configuration"></a>Configuração da VM

* 470 VMs no cluster principal.
* Todas as VMs com o disco VHDX.
* VMs a executar cargas de trabalho resumidas na tabela. Todos foram criados com modelos do VMM.

| Carga de trabalho | # VMs | Quantidade mínima de RAM (GB) | RAM máxima (GB) | Tamanho de disco lógico (GB) por VM | IOPS máximo |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Servidor do Exchange |71 |1 |4 |552 |10 |
| Servidor de Ficheiros |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Servidor Web |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Recuperação de sites

* Recuperação de sites foi configurada para o local para a proteção no local
* O servidor VMM tem quatro nuvens configuradas, que contém os servidores de cluster do Hyper-V e as respetivas VMs.

| Cloud do VMM principal | VMs protegidas | Frequência de replicação | Pontos de recuperação adicionais |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 min |Nenhuma |
| PrimaryCloudRpo30s |47 |30 seg |Nenhuma |
| PrimaryCloudRpo30sArp1 |47 |30 seg |1 |
| PrimaryCloudRpo5m |235 |5 mins |Nenhuma |

### <a name="performance-metrics"></a>Métricas de desempenho

A tabela resume os contadores que foram medidos na implementação e métricas de desempenho.

| Métrica | Contador |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Memória disponível |MBytes de \Memory\Available |
| IOPS |\PhysicalDisk ( total) \Disk transferências/seg. |
| VM (IOPS) operações/seg de leitura |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| Operações de escrita (IOPS) de VM/seg |Dispositivo de armazenamento virtual \Hyper-V (\<VHD >) \Write operações/S |
| Débito de leitura de VM |Dispositivo de armazenamento virtual \Hyper-V (\<VHD >) \Read Bytes/seg |
| Débito de escrita da VM |Dispositivo de armazenamento virtual \Hyper-V (\<VHD >) \Write Bytes/seg |

## <a name="next-steps"></a>Passos Seguintes

[Configurar a replicação](hyper-v-vmm-disaster-recovery.md)
