---
title: Computação de alto desempenho em VMs da série H-máquinas virtuais do Azure
description: Saiba mais sobre os recursos e as funcionalidades das VMs da série H otimizadas para HPC.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271022"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Computação de alto desempenho em VMs da série H

O HPC (computação de alto desempenho) nas VMs da série HB e da HC permite o desempenho de HPC mais otimizado de qualquer VM no Azure. As VMs otimizadas para HPC são usadas para resolver alguns dos problemas matemáticos mais difíceis, como: dinâmica de fluidos, simulações de petróleo e gás e modelagem meteorológico.

Este artigo aborda alguns dos principais recursos das VMs da série HB e da HC, por que essas VMs executam bem em cenários de HPC e como começar.

## <a name="features-and-capabilities"></a>Funcionalidades e capacidades

As VMs da série HB e HC são projetadas para fornecer o melhor desempenho de HPC, escalabilidade de MPI (Message passing interface) e eficiência de custo para cargas de trabalho do HPC.

### <a name="message-passing-interface"></a>Interface de transmissão de mensagens

As séries HB e HC oferecem suporte a quase todos os tipos e versões MPI. Alguns dos tipos MPI mais comuns, com suporte, são: OpenMP, MVAPICH2, Platform MPI, Intel MPI e todos os verbos de RDMA (acesso remoto direto à memória). Para obter mais informações, consulte [Configurar a interface de passagem de mensagens para HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA e InfiniBand

A interface RDMA é padrão nas VMs da série HB e da HC. As instâncias compatíveis com RDMA se comunicam por meio de uma rede InfiniBand, operando em EDR (taxas de dados aprimoradas) para máquinas virtuais da série HB e HC. As instâncias compatíveis com RDMA podem aumentar a escalabilidade e o desempenho de alguns aplicativos MPI.

A configuração InfiniBand que dá suporte a VMs da série HB e HC são árvores de gordura sem bloqueio com um design de pouco diâmetro para desempenho consistente de RDMA.

Consulte [habilitar InfiniBand](enable-infiniband.md) para saber mais sobre como configurar o InfiniBand em suas VMs da série HB ou HC.

## <a name="get-started"></a>Começar

Primeiro, decida qual VM da série H você pretende usar. Para obter detalhes sobre VMs otimizadas para o HPC, consulte Visão geral da [série HB](hb-series-overview.md) e [visão geral da série HC](hc-series-overview.md). Para obter especificações, consulte [tamanhos de VM de computação de alto desempenho](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

Depois de selecionar e criar uma VM para seu aplicativo, você precisará configurá-la habilitando a InfiniBand. Para saber como habilitar o InfiniBand em VMs do Windows e Linux, consulte [habilitar InfiniBand](enable-infiniband.md).

Um componente crítico das cargas de trabalho do HPC é MPI. As séries HB e HC oferecem suporte a quase todos os tipos e versões MPI. Para obter mais informações, consulte [Configurar a interface de passagem de mensagens para HPC](setup-mpi.md).

Depois de escolher sua série de VMs, configure o InfiniBand e o MPI, você estará pronto para começar a criar suas cargas de trabalho do HPC.

## <a name="next-steps"></a>Passos seguintes

- Examine a visão geral da série [HB](hb-series-overview.md) e da [HC-Series](hc-series-overview.md) para saber mais sobre as principais diferenças e especificações.

- Para uma visão de nível mais alto, a exibição arquitetônica da execução de cargas de trabalho do HPC, consulte [computação de alto desempenho (HPC) no Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
