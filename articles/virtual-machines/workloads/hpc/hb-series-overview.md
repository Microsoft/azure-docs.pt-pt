---
title: Visão geral da série VM da série HB - Máquinas Virtuais Azure / Microsoft Docs
description: Conheça o suporte de pré-visualização para o tamanho VM da série HB em Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707763"
---
# <a name="hb-series-virtual-machines-overview"></a>Visão geral das máquinas virtuais da série HB

Maximizar o desempenho da aplicação de alta performance computacional (HPC) na AMD EPYC requer uma localização de memória de abordagem pensada e colocação de processos. Abaixo delineamos a arquitetura AMD EPYC e a nossa implementação no Azure para aplicações hpc. Usaremos o termo "pNUMA" para se referir a um domínio físico da NUM, e "vNUMA" para se referir a um domínio NUMA virtualizado.

Fisicamente, uma série HB é 2 * 32-core EPYC 7551 CPUs para um total de 64 núcleos físicos. Estes 64 núcleos estão divididos em 16 domínios pNUMA (8 por tomada), cada um dos quais são quatro núcleos e conhecidos como "CpU Complex" (ou "CCX"). Cada CCX tem a sua própria cache L3, que é como um SISTEMA verá um limite pNUMA/vNUMA. Um par de CCXs adjacentes partilha o acesso a dois canais de DRAM físico (32 GB de DRAM em servidores da série HB).

Para proporcionar espaço para o hipervisor Azure funcionar sem interferir com o VM, reservamos o domínio físico pNUMA 0 (o primeiro CCX). Em seguida, atribuímos os domínios pNUMA 1-15 (as restantes unidades CCX) para o VM. O VM verá:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`núcleos por VM

O VM, por si só, não sabe que o pNUM 0 não lhe foi dado. O VM entende o pNUM 1-15 como vNUMA 0-14, com 7 vNUMA na vSocket 0 e 8 vNUMA na vSocket 1. Embora isto seja assimétrico, o seu Sistema operativo deve arrancar e funcionar normalmente. Mais tarde neste guia, instruímos a melhor forma de executar aplicações de MPI neste layout ASsimétrico DE EM.

O processo funcionará em VMs da série HB porque expomos o silício subjacente como está ao VM convidado. Recomendamos vivamente o processo de fixação para um desempenho e consistência ideais.

Veja mais na [arquitetura AMD EPYC](https://bit.ly/2Epv3kC) e [arquiteturas multi-chip](https://bit.ly/2GpQIMb) no LinkedIn. Para obter informações mais detalhadas, consulte o Guia de [Afinação do HPC para processadores AMD EPYC](https://bit.ly/2T3AWZ9).

O diagrama seguinte mostra a segregação de núcleos reservados para o Azure Hypervisor e o VM da série HB.

![Segregação de núcleos reservados para Azure Hypervisor e VM da série HB](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações HW                | VM da série HB                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 60 (SMT desativado)                |
| CPU                              | AMD EPYC 7551*                   |
| Frequência CPU (não-AVX)          | ~2,55 GHz (single + todos os núcleos)   |
| Memória                           | 4 GB/núcleo (total de 240)            |
| Disco Local                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Rede                          | 50 Gb Ethernet (40 Gb utilizável) Azure segundo Gen SmartNIC*** |

## <a name="software-specifications"></a>Especificações de software

| Especificações SW           |VM da série HB           |
|-----------------------------|-----------------------|
| Tamanho max MPI Trabalho            | 6000 núcleos (100 conjuntos de escala de máquinavirtual) 12000 núcleos (200 conjuntos de escala de máquinavirtual)  |
| Suporte mpi                 | MVAPICH2, OpenMPI, MPICH, Plataforma MPI, Intel MPI  |
| Quadros adicionais       | Comunicação Unificada X, libfabric, PGAS |
| Suporte de armazenamento Azure       | Std + Premium (max 4 discos) |
| Suporte so os o   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Suporte Azure CycleCloud    | Sim                         |
| Suporte de lote azure         | Sim                         |

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os tamanhos de VM HPC para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) em Azure.

* Saiba mais sobre [o HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) em Azure.
