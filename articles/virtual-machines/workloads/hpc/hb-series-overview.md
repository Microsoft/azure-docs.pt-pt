---
title: Visão geral da série HB VM - Azure Virtual Machines | Microsoft Docs
description: Saiba mais sobre o suporte de pré-visualização para o tamanho VM da série HB em Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 2f5dddd3d59ebe778d577176e439528a86bb42a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802602"
---
# <a name="hb-series-virtual-machines-overview"></a>Visão geral das máquinas virtuais da série HB

Maximizar o desempenho da aplicação de computação de alto desempenho (HPC) na AMD EPYC requer uma abordagem ponderada da localidade da memória e colocação do processo. Abaixo descrevemos a arquitetura AMD EPYC e a nossa implementação no Azure para aplicações HPC. Usaremos o termo "pNUMA" para se referir a um domínio NUMA físico, e "vNUMA" para se referir a um domínio NUMA virtualizado.

Fisicamente, um servidor [da série HB](../../hb-series.md) é 2 * 32-core EPYC 7551 CPUs para um total de 64 núcleos físicos. Estes 64 núcleos estão divididos em 16 domínios pNUMA (8 por tomada), cada um dos quais é de quatro núcleos e é conhecido como "Complexo cpu" (ou "CCX"). Cada CCX tem a sua própria cache L3, que é como um SISTEMA verá um limite pNUMA/vNUMA. Um par de CCXs adjacentes partilha o acesso a dois canais de DRAM físico (32 GB de DRAM em servidores da série HB).

Para proporcionar espaço para o hipervisor Azure funcionar sem interferir com o VM, reservamos o domínio físico pNUMA 0 (o primeiro CCX). Em seguida, atribuímos os domínios pNUMA 1-15 (as restantes unidades CCX) para o VM. O VM verá:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` núcleos por VM

O VM, por si só, não sabe que o pNUMA 0 não lhe foi dado. O VM entende o pNUMA 1-15 como vNUMA 0-14, com 7 vNUMA no vSocket 0 e 8 vNUMA no vSocket 1. Embora isto seja assimétrico, o seu SO deve arrancar e funcionar normalmente. Mais tarde neste guia, instruímos a melhor forma de executar aplicações mpi neste layout ASI assimétrico.

A fixação de processos funcionará em VMs da série HB porque expomos o silício subjacente como está ao VM convidado. Recomendamos vivamente a fixação de processos para um melhor desempenho e consistência.

O diagrama seguinte mostra a segregação de núcleos reservados para O Hipervisor Azure e o VM da série HB.

![Segregação de núcleos reservados para Azure Hypervisor e HB-series VM](./media/architecture/hb-segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações de hardware                | VM da série HB                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 60 (SMT desativado)                |
| CPU                              | AMD EPYC 7551                    |
| Frequência CPU (não-AVX)          | ~2,55 GHz (single + todos os núcleos)   |
| Memória                           | 4 GB/núcleo (total de 240 GB)         |
| Disco Local                       | 700 GB SSD                       |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 |
| Rede                          | 50 Gb Ethernet (40 Gb utilizável) Azure segundo Gen SmartNIC |

## <a name="software-specifications"></a>Especificações de software

| Especificações de Software           |VM da série HB           |
|-----------------------------|-----------------------|
| Tamanho do trabalho de MPI Max            | 18000 núcleos (300 VMs numa única escala de máquina virtual definida com singlePlacementGroup=verdadeiro)  |
| Suporte MPI                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, Plataforma MPI  |
| Quadros Adicionais       | UCX, libfabric, PGAS |
| Suporte de armazenamento Azure       | Discos Standard e Premium (máximo 4 discos) |
| Apoio ao SO para SRIOV RDMA   | CentOS/RHEL 7.6+, Ubuntu 16.04+, SLES 12 SP4+, WinServer 2016+  |
| Apoio orquestrador        | CycleCloud, Lote, AKS; [opções de configuração de cluster](../../sizes-hpc.md#cluster-configuration-options) |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [arquitetura AMD EPYC](https://bit.ly/2Epv3kC) e [arquiteturas multi-chip.](https://bit.ly/2GpQIMb) Para obter informações mais detalhadas, consulte o [Guia de Afinação do HPC para processadores AMD EPYC](https://bit.ly/2T3AWZ9).
- Leia sobre os últimos anúncios, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs comunitários Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
