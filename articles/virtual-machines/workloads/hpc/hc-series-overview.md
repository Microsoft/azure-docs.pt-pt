---
title: Visão geral da série HC VM - Azure Virtual Machines / Microsoft Docs
description: Saiba mais sobre o suporte de pré-visualização para o tamanho VM da série HC em Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906737"
---
# <a name="hc-series-virtual-machine-overview"></a>Visão geral da máquina virtual da série HC

Maximizar o desempenho da aplicação HPC em processadores Escaláveis Intel Xeon requer uma abordagem pensada para processar a colocação nesta nova arquitetura. Aqui, delineamos a nossa implementação em VMs da série Azure HC para aplicações HPC. Usaremos o termo "pNUMA" para se referir a um domínio NUMA físico, e "vNUMA" para se referir a um domínio NUMA virtualizado. Da mesma forma, usaremos o termo "pCore" para se referir aos núcleos físicos do CPU, e "vCore" para se referir aos núcleos de CPU virtualizados.

Fisicamente, um servidor HC é 2 * 24-core Intel Xeon Platinum 8168 CPUs para um total de 48 núcleos físicos. Cada CPU é um único domínio pNUMA, e tem acesso unificado a seis canais de DRAM. Os CPUs Intel Xeon Platinum apresentam uma cache L2 maior de 4x do que nas gerações anteriores (256 KB/core -> 1 MB/core), reduzindo também a cache L3 em comparação com cpus intel anteriores (2,5 MB/core -> 1,375 MB/core).

A topologia acima também leva para a configuração do hipervisor da série HC. Para dar espaço para o hipervisor Azure funcionar sem interferir com o VM, reservamos pCores 0-1 e 24-25 (ou seja, os primeiros 2 pCores em cada tomada). Em seguida, atribuímos domínios pNUMA todos os núcleos restantes para o VM. Assim, o VM verá:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`núcleos por VM

O VM não tem conhecimento de que os pCores 0-1 e 24-25 não lhe tenham sido dados. Assim, expõe cada vNUMA como se tivesse 22 núcleos nativo.

Intel Xeon Platinum, Gold e Silver CPUs também introduzem uma rede de malha 2D on-die para comunicação dentro e externa à tomada cpu. Recomendamos vivamente a fixação de processos para um melhor desempenho e consistência. A fixação de processos funcionará em VMs da série HC porque o silício subjacente está exposto como está para o VM convidado. Para saber mais, consulte [a arquitetura Intel Xeon SP.](https://bit.ly/2RCYkiE)

O diagrama seguinte mostra a segregação de núcleos reservados para O Hipervisor Azure e o VM da série HC.

![Segregação de núcleos reservados para Azure Hypervisor e VM série HC](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações de hardware          | VM da série HC                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 44 (HT desativado)                 |
| CPU                              | Intel Xeon Platinum 8168*        |
| Frequência CPU (não-AVX)          | 3,7 GHz (núcleo único), 2.7-3.4 GHz (todos os núcleos) |
| Memória                           | 8 GB/núcleo (352 no total)            |
| Disco Local                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Rede                          | 50 Gb Ethernet (40 Gb utilizável) Azure segundo Gen SmartNIC*** |

## <a name="software-specifications"></a>Especificações de software

| Especificações de Software     | VM da série HC          |
|-----------------------------|-----------------------|
| Tamanho do trabalho de MPI Max            | 13200 núcleos (300 VMs num único VMSS com singlePlacementGroup=verdadeiro) |
| Suporte MPI                 | MVAPICH2, OpenMPI, MPICH, Plataforma MPI, Intel MPI  |
| Quadros Adicionais       | Comunicação Unificada X, libfabric, PGAS |
| Suporte de armazenamento Azure       | Std + Premium (máx. 4 discos) |
| Apoio ao SO para SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Suporte Azure CycleCloud    | Sim                         |
| Suporte a lote de Azure         | Sim                         |

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre os tamanhos HPC VM para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) em Azure.

* Saiba mais sobre [o HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) em Azure.
