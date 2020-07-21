---
title: Visão geral da série HB VM - Azure Virtual Machines Microsoft Docs
description: Saiba mais sobre o suporte de pré-visualização para o tamanho VM da série HB em Azure.
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
ms.openlocfilehash: fed5606da84d8311785752cc8319b7a3c642c1f5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86508037"
---
# <a name="hb-series-virtual-machines-overview"></a>Visão geral das máquinas virtuais da série HB

Maximizar o desempenho da aplicação de computação de alto desempenho (HPC) na AMD EPYC requer uma abordagem ponderada da localidade da memória e colocação do processo. Abaixo descrevemos a arquitetura AMD EPYC e a nossa implementação no Azure para aplicações HPC. Usaremos o termo "pNUMA" para se referir a um domínio NUMA físico, e "vNUMA" para se referir a um domínio NUMA virtualizado.

Fisicamente, uma série HB é 2 * 32-core EPYC 7551 CPUs para um total de 64 núcleos físicos. Estes 64 núcleos estão divididos em 16 domínios pNUMA (8 por tomada), cada um dos quais é de quatro núcleos e é conhecido como "Complexo cpu" (ou "CCX"). Cada CCX tem a sua própria cache L3, que é como um SISTEMA verá um limite pNUMA/vNUMA. Um par de CCXs adjacentes partilha o acesso a dois canais de DRAM físico (32 GB de DRAM em servidores da série HB).

Para proporcionar espaço para o hipervisor Azure funcionar sem interferir com o VM, reservamos o domínio físico pNUMA 0 (o primeiro CCX). Em seguida, atribuímos os domínios pNUMA 1-15 (as restantes unidades CCX) para o VM. O VM verá:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`núcleos por VM

O VM, por si só, não sabe que o pNUMA 0 não lhe foi dado. O VM entende o pNUMA 1-15 como vNUMA 0-14, com 7 vNUMA no vSocket 0 e 8 vNUMA no vSocket 1. Embora isto seja assimétrico, o seu SO deve arrancar e funcionar normalmente. Mais tarde neste guia, instruímos a melhor forma de executar aplicações mpi neste layout ASI assimétrico.

A fixação de processos funcionará em VMs da série HB porque expomos o silício subjacente como está ao VM convidado. Recomendamos vivamente a fixação de processos para um melhor desempenho e consistência.

Veja mais sobre [arquitetura AMD EPYC](https://bit.ly/2Epv3kC) e [arquiteturas multi-chips](https://bit.ly/2GpQIMb) no LinkedIn. Para obter informações mais detalhadas, consulte o [Guia de Afinação do HPC para processadores AMD EPYC](https://bit.ly/2T3AWZ9).

O diagrama seguinte mostra a segregação de núcleos reservados para O Hipervisor Azure e o VM da série HB.

![Segregação de núcleos reservados para Azure Hypervisor e HB-series VM](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações da HW                | VM da série HB                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 60 (SMT desativado)                |
| CPU                              | AMD EPYC 7551*                   |
| Frequência CPU (não-AVX)          | ~2,55 GHz (single + todos os núcleos)   |
| Memória                           | 4 GB/núcleo (240 no total)            |
| Disco Local                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Rede                          | 50 Gb Ethernet (40 Gb utilizável) Azure segundo Gen SmartNIC*** |

## <a name="software-specifications"></a>Especificações de software

| Especificações sw           |VM da série HB           |
|-----------------------------|-----------------------|
| Tamanho do trabalho de MPI Max            | 6000 núcleos (100 conjuntos de balança de máquinas virtuais) 12000 núcleos (200 conjuntos de balanças de máquinas virtuais)  |
| Suporte MPI                 | MVAPICH2, OpenMPI, MPICH, Plataforma MPI, Intel MPI  |
| Quadros Adicionais       | Comunicação Unificada X, libfabric, PGAS |
| Suporte de armazenamento Azure       | Std + Premium (máx. 4 discos) |
| Apoio ao SO para SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Suporte Azure CycleCloud    | Sim                         |
| Suporte a lote de Azure         | Sim                         |

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre os tamanhos HPC VM para [Linux](../../sizes-hpc.md) e [Windows](../../sizes-hpc.md) em Azure.

* Saiba mais sobre [o HPC](/azure/architecture/topics/high-performance-computing/) em Azure.
