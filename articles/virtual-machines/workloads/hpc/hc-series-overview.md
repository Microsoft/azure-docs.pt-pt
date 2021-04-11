---
title: Visão geral da série HC VM - Azure Virtual Machines| Microsoft Docs
description: Saiba mais sobre o suporte de pré-visualização para o tamanho VM da série HC em Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: c251634710811820ba920b72c1759938758f5d2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802828"
---
# <a name="hc-series-virtual-machine-overview"></a>Visão geral da máquina virtual da série HC

Maximizar o desempenho da aplicação HPC em processadores Escaláveis Intel Xeon requer uma abordagem pensada para processar a colocação nesta nova arquitetura. Aqui, delineamos a nossa implementação em VMs da série Azure HC para aplicações HPC. Usaremos o termo "pNUMA" para se referir a um domínio NUMA físico, e "vNUMA" para se referir a um domínio NUMA virtualizado. Da mesma forma, usaremos o termo "pCore" para se referir aos núcleos físicos do CPU, e "vCore" para se referir aos núcleos de CPU virtualizados.

Fisicamente, um servidor [da série HC](../../hc-series.md) é 2 * 24-core Intel Xeon Platinum 8168 CPUs para um total de 48 núcleos físicos. Cada CPU é um único domínio pNUMA, e tem acesso unificado a seis canais de DRAM. Os CPUs Intel Xeon Platinum apresentam uma cache L2 maior de 4x do que nas gerações anteriores (256 KB/core -> 1 MB/core), reduzindo também a cache L3 em comparação com cpus intel anteriores (2,5 MB/core -> 1,375 MB/core).

A topologia acima também leva para a configuração do hipervisor da série HC. Para dar espaço para o hipervisor Azure funcionar sem interferir com o VM, reservamos pCores 0-1 e 24-25 (ou seja, os primeiros 2 pCores em cada tomada). Em seguida, atribuímos domínios pNUMA todos os núcleos restantes para o VM. Assim, o VM verá:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` núcleos por VM

O VM não tem conhecimento de que os pCores 0-1 e 24-25 não lhe tenham sido dados. Assim, expõe cada vNUMA como se tivesse 22 núcleos nativo.

Intel Xeon Platinum, Gold e Silver CPUs também introduzem uma rede de malha 2D on-die para comunicação dentro e externa à tomada cpu. Recomendamos vivamente a fixação de processos para um melhor desempenho e consistência. A fixação de processos funcionará em VMs da série HC porque o silício subjacente está exposto como está para o VM convidado.

O diagrama seguinte mostra a segregação de núcleos reservados para O Hipervisor Azure e o VM da série HC.

![Segregação de núcleos reservados para Azure Hypervisor e VM série HC](./media/architecture/hc-segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações de hardware          | VM da série HC                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 44 (HT desativado)                 |
| CPU                              | Intel Xeon Platinum 8168         |
| Frequência CPU (não-AVX)          | 3,7 GHz (núcleo único), 2.7-3.4 GHz (todos os núcleos) |
| Memória                           | 8 GB/núcleo (352 no total)            |
| Disco Local                       | 700 GB SSD                       |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5   |
| Rede                          | 50 Gb Ethernet (40 Gb utilizável) Azure segundo Gen SmartNIC    |

## <a name="software-specifications"></a>Especificações de software

| Especificações de Software     |VM da série HC           |
|-----------------------------|-----------------------|
| Tamanho do trabalho de MPI Max            | 13200 núcleos (300 VMs numa única escala de máquina virtual definida com singlePlacementGroup=verdadeiro)  |
| Suporte MPI                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, Plataforma MPI  |
| Quadros Adicionais       | UCX, libfabric, PGAS |
| Suporte de armazenamento Azure       | Discos Standard e Premium (máximo 4 discos) |
| Apoio ao SO para SRIOV RDMA   | CentOS/RHEL 7.6+, Ubuntu 16.04+, SLES 12 SP4+, WinServer 2016+  |
| Apoio orquestrador        | CycleCloud, Lote, AKS; [opções de configuração de cluster](../../sizes-hpc.md#cluster-configuration-options)  |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a arquitetura Intel Xeon SP.](https://software.intel.com/content/www/us/en/develop/articles/intel-xeon-processor-scalable-family-technical-overview.html)
- Leia sobre os últimos anúncios, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs comunitários Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
