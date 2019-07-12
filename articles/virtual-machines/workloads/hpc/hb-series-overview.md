---
title: Descrição geral de VM série HB - máquinas virtuais do Azure | Documentos da Microsoft
description: Saiba mais sobre o suporte de pré-visualização para o tamanho VM da série HB no Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707763"
---
# <a name="hb-series-virtual-machines-overview"></a>Descrição geral de máquinas virtuais de série HB

Maximizar o desempenho de aplicações de alto desempenho (HPC) de computação no AMD EPYC requer um posicionamento de localidade e o processo da memória de abordagem consciente. Abaixo temos descrevem a arquitetura de AMD EPYC e a nossa implementação do mesmo no Azure para aplicações de HPC. Utilizamos o termo "pNUMA" para fazer referência a um domínio físico de NUMA e "vNUMA" para fazer referência a um domínio NUMA virtualizado.

Fisicamente, uma série de HB é 2 * 32 núcleos EPYC 7551 CPUs para um total de 64 núcleos físicos. Estes 64 núcleos são divididos em 16 domínios de pNUMA (8 por socket), cada uma delas é de quatro núcleos e conhecidos como um "CPU complexos" (ou "CCX"). Cada CCX tem seu próprio cache L3, que é como um sistema operacional irá ver um limite de pNUMA/vNUMA. Um par de CCXs adjacentes partilha o acesso a dois canais de DRAM físico (32 GB de DRAM nos servidores de série HB).

Para fornecer espaço para o hipervisor do Azure operar sem interferir com a VM, a Microsoft reserva-pNUMA físico domínio 0 (o primeiro CCX). Em seguida, Atribuímos pNUMA domínios 1 a 15 (as restantes unidades CCX) para a VM. Verá a VM:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` núcleos por VM

A VM, por si só, não sabe que dessa pNUMA 0 não estava tendo em conta a ele. A VM compreende pNUMA 1 a 15 como vNUMA 0 a 14, com 7 vNUMA no vNUMA vSocket 0 e 8 no vSocket 1. Embora isso seja assimétricas, seu sistema operacional deve efetuar o arranque e funcionar normalmente. Mais tarde neste guia, que eu o instruir a melhor maneira para executar aplicações MPI neste esquema NUMA assimétrica.

A afixação de processo irá funcionar em VMs de série HB, uma vez que expomos silicone subjacente como-é a VM do convidado. Recomendamos vivamente a afixação de processo para otimizar o desempenho e consistência.

Ver mais no [arquitetura de AMD EPYC](https://bit.ly/2Epv3kC) e [arquiteturas de chips Multi](https://bit.ly/2GpQIMb) no LinkedIn. Para obter mais informações, consulte a [guia de ajuste do HPC para processadores de EPYC AMD](https://bit.ly/2T3AWZ9).

O diagrama seguinte mostra a diferenciação de núcleos reservados para o hipervisor do Azure e a VM de série HB.

![Segregação de núcleos reservados para o hipervisor do Azure e a VM de série HB](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações de HW                | VM da série HB                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 60 (SMT desativada)                |
| CPU                              | AMD EPYC 7551*                   |
| Frequência de CPU (não AVX)          | ~2.55 GHz (único + todos os núcleos)   |
| Memória                           | 4 GB/núcleos (total de 240)            |
| Disco Local                       | NVMe de 700 GB                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 * * |
| Rede                          | 50 Gb Ethernet (40 Gb utilizável) do Azure segundo fins SmartNIC *** |

## <a name="software-specifications"></a>Especificações de software

| Especificações SW           |VM da série HB           |
|-----------------------------|-----------------------|
| Tamanho de tarefa de MPI máx.            | núcleos de 12000 6000 núcleos (100 conjuntos de dimensionamento de máquina virtual) (200 conjuntos de dimensionamento de máquina virtual)  |
| Suporte MPI                 | MVAPICH2, OpenMPI, MPICH, plataforma MPI, Intel MPI  |
| Estruturas adicionais       | X de comunicação unificada, libfabric, PGAS |
| Suporte de armazenamento do Azure       | E padrão + Premium (4 discos máx.) |
| Suporte de SO para SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Suporte de CycleCloud do Azure    | Sim                         |
| Suporte do Azure Batch         | Sim                         |

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre tamanhos de VM de HPC para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) no Azure.

* Saiba mais sobre [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
