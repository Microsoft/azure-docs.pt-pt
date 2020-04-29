---
title: Visão geral da série HC VM - Máquinas Virtuais Azure/ Microsoft Docs
description: Conheça o suporte de pré-visualização para o tamanho VM da série HC em Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906737"
---
# <a name="hc-series-virtual-machine-overview"></a>Visão geral da máquina virtual da série HC

Maximizar o desempenho da aplicação HPC em Processadores Scalable Intel Xeon requer uma abordagem ponderada para processar a colocação nesta nova arquitetura. Aqui, delineamos a nossa implementação em VMs da série Azure HC para aplicações HPC. Usaremos o termo "pNUMA" para se referir a um domínio físico da NUM, e "vNUMA" para se referir a um domínio NUMA virtualizado. Da mesma forma, usaremos o termo "pCore" para se referir a núcleos de CPU físicos, e "vCore" para se referir a núcleos de CPU virtualizados.

Fisicamente, um servidor HC é 2 * 24 núcleoIntel Xeon Platinum 8168 CPUs para um total de 48 núcleos físicos. Cada CPU é um domínio único do pNUMA, e tem acesso unificado a seis canais de DRAM. A Intel Xeon Platinum CPUs possui uma cache L2 4x maior do que nas gerações anteriores (256 KB/core -> 1 MB/core), reduzindo também a cache L3 em comparação com as CPUs anteriores da Intel (2,5 MB/core -> 1,375 MB/core).

A topologia acima leva para a configuração hipervisor da série HC também. Para proporcionar espaço para o hipervisor Azure funcionar sem interferir com o VM, reservamos pCores 0-1 e 24-25 (isto é, os primeiros 2 pCores em cada tomada). Em seguida, atribuímos domínios pNUMA todos os núcleos restantes ao VM. Assim, o VM verá:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`núcleos por VM

O VM não tem conhecimento de que os pCores 0-1 e 24-25 não lhe foram dados. Assim, expõe cada vNUMA como se tivesse 22 núcleos.

Intel Xeon Platinum, Gold e Silver CPUs também introduzem uma rede de malha 2D on-die para comunicação dentro e externa à tomada CPU. Recomendamos vivamente o processo de fixação para um desempenho e consistência ideais. O processo de fixação funcionará em VMs da série HC porque o silício subjacente é exposto como está ao VM convidado. Para saber mais, consulte [a arquitetura Intel Xeon SP.](https://bit.ly/2RCYkiE)

O diagrama seguinte mostra a segregação de núcleos reservados para o Azure Hypervisor e o VM da série HC.

![Segregação de núcleos reservados para Azure Hypervisor e série HC VM](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações de hardware          | Série HC VM                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 44 (HT desativado)                 |
| CPU                              | Intel Xeon Platinum 8168*        |
| Frequência CPU (não-AVX)          | 3,7 GHz (núcleo único), 2.7-3,4 GHz (todos os núcleos) |
| Memória                           | 8 GB/núcleo (total de 352)            |
| Disco Local                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Rede                          | 50 Gb Ethernet (40 Gb utilizável) Azure segundo Gen SmartNIC*** |

## <a name="software-specifications"></a>Especificações de software

| Especificações de software     | Série HC VM          |
|-----------------------------|-----------------------|
| Tamanho max MPI Trabalho            | 13200 núcleos (300 VMs num único VMSS com singlePlacementGroup=true) |
| Suporte mpi                 | MVAPICH2, OpenMPI, MPICH, Plataforma MPI, Intel MPI  |
| Quadros adicionais       | Comunicação Unificada X, libfabric, PGAS |
| Suporte de armazenamento Azure       | Std + Premium (max 4 discos) |
| Suporte so os o   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Suporte Azure CycleCloud    | Sim                         |
| Suporte de lote azure         | Sim                         |

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os tamanhos de VM HPC para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) em Azure.

* Saiba mais sobre [o HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) em Azure.
