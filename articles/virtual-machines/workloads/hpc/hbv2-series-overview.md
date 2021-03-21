---
title: Visão geral da série VM da série HBv2 - Azure Virtual Machines | Microsoft Docs
description: Saiba mais sobre o tamanho VM da série HBv2 em Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6648f77c5eacf40f848bc9b24aa6e257d8adf626
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674645"
---
# <a name="hbv2-series-virtual-machine-overview"></a>Visão geral da máquina virtual da série HBv2 

 
Maximizar o desempenho da aplicação de computação de alto desempenho (HPC) na AMD EPYC requer uma abordagem ponderada da localidade da memória e colocação do processo. Abaixo descrevemos a arquitetura AMD EPYC e a nossa implementação no Azure para aplicações HPC. Usaremos o termo **pNUMA** para se referir a um domínio NUMA físico, e **vNUMA** para se referir a um domínio NUMA virtualizado. 

Fisicamente, um servidor [da série HBv2](../../hbv2-series.md) é de 2 * 64-core EPYC 7742 CPUs para um total de 128 núcleos físicos. Estes 128 núcleos são divididos em 32 domínios pNUMA (16 por tomada), cada um dos quais é de 4 núcleos e denominado pela AMD como **Um Complexo Core** (ou **CCX).** Cada CCX tem a sua própria cache L3, que é como um SISTEMA verá um limite pNUMA/vNUMA. Quatro CCX adjacentes partilham acesso a 2 canais de DRAM físico. 

Para proporcionar espaço para o hipervisor Azure funcionar sem interferir com o VM, reservamos os domínios físicos de pNUMA 0 e 16 (ou seja, o primeiro CCX de cada tomada cpU). Todos os restantes 30 domínios pNUMA são atribuídos ao VM, altura em que se tornam vNUMA. Assim, o VM verá:

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` núcleos por VM 

A própria VM não tem consciência de que os pNUMA 0 e 16 estão reservados. Enumera o vNUMA que vê como 0-29, com 15 vNUMA por tomada simetricamente, vNUMA 0-14 no vSocket 0, e vNUMA 15-29 em vSocket 1. Na secção seguinte, existem instruções sobre a melhor forma de executar aplicações de MPI neste layout ASI assimétrico. 

A fixação de processos funcionará em VMs da série HBv2 porque expomos o silício subjacente como está ao VM convidado. Recomendamos vivamente a fixação de processos para um melhor desempenho e consistência. 


## <a name="hardware-specifications"></a>Especificações de hardware 

| Especificações de hardware          | VM da série HBv2                   | 
|----------------------------------|----------------------------------|
| Núcleos                            | 120 (SMT desativado)               | 
| CPU                              | AMD EPYC 7742                    | 
| Frequência CPU (não-AVX)          | ~3.1 GHz (single + todos os núcleos)    | 
| Memória                           | 4 GB/núcleo (total de 480 GB)         | 
| Disco Local                       | 960 GB NVMe (bloco), 480 GB SSD (ficheiro de página) | 
| Infiniband                       | 200 Gb/s EDR Mellanox ConnectX-6 | 
| Rede                          | 50 Gb/s Ethernet (40 Gb/s utilizável) Azure segundo Gen SmartNIC | 


## <a name="software-specifications"></a>Especificações de software 

| Especificações de Software     | VM da série HBv2                                            | 
|-----------------------------|-----------------------------------------------------------|
| Tamanho do trabalho de MPI Max            | 36000 núcleos (300 VMs numa única escala de máquina virtual definida com singlePlacementGroup=verdadeiro) |
| Suporte MPI                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, Plataforma MPI  |
| Quadros Adicionais       | Comunicação Unificada X, libfabric, PGAS                  |
| Suporte de armazenamento Azure       | Discos Standard e Premium (máximo 8 discos)              |
| Apoio ao SO para SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+           |
| Apoio orquestrador        | CycleCloud, Lote                                         | 


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [arquitetura AMD EPYC](https://bit.ly/2Epv3kC) e [arquiteturas multi-chip.](https://bit.ly/2GpQIMb) Para obter informações mais detalhadas, consulte o [Guia de Afinação do HPC para processadores AMD EPYC](https://bit.ly/2T3AWZ9).
- Leia sobre os últimos anúncios e alguns exemplos de HPC nos [Blogs comunitários Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).