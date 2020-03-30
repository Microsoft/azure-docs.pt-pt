---
title: Computação de alto desempenho em VMs da série H - Máquinas Virtuais Azure
description: Conheça as funcionalidades e capacidades dos VMs da série H otimizados para hPC.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76271022"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Computação de alto desempenho em VMs da série H

A computação de alto desempenho (HPC) em VMs de sérieHB e HC permitem o desempenho hpc mais otimizado de quaisquer VMs em Azure. Os VMs otimizados do HPC são usados para resolver alguns dos problemas matemáticos mais difíceis, tais como: dinâmica de fluidos, simulações de petróleo e gás e modelação meteorológica.

Este artigo abrange algumas características-chave das séries HB e VMs da série HC, porque estes VMs têm um bom desempenho em cenários de HPC e como começar.

## <a name="features-and-capabilities"></a>Funcionalidades e capacidades

As séries HB e os VMs da série HC são projetados para fornecer o melhor desempenho hpc, interface de passagem de mensagens (MPI) e eficiência de custos para cargas de trabalho HPC.

### <a name="message-passing-interface"></a>Interface de passagem de mensagem

As séries HB e as séries HC suportam quase todos os tipos e versões de MPI. Alguns dos tipos de MPI mais comuns e suportados são: OpenMPI, MVAPICH2, Plataforma MPI, Intel MPI e todos os verbos de acesso à memória direta remota (RDMA). Para mais informações, consulte Configurar interface de [passagem de mensagens para HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA e InfiniBand

A interface RDMA é padrão em VMs da série HB e HC. As instâncias capazes de RDMA comunicam através de uma rede InfiniBand, operando a taxas de dados melhoradas (EDR) para máquinas virtuais da série HB e hc-series. Os casos de RDMA podem aumentar a escalabilidade e desempenho de algumas aplicações de MPI.

A configuração InfiniBand que suporta as séries HB e os VMs da série HC são árvores gordas que não bloqueiam com um design de baixo diâmetro para um desempenho RDMA consistente.

Consulte a [Enable InfiniBand](enable-infiniband.md) para saber mais sobre a criação da InfiniBand nas suas séries HB ou VMs da série HC.

## <a name="get-started"></a>Introdução

Primeiro, decida qual vm da série H vai usar. Para mais detalhes sobre VMs otimizados hPC, consulte a [visão geral da série HB](hb-series-overview.md) e a visão geral da série [HC](hc-series-overview.md). Para especificações, consulte [tamanhos vM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)de computação de alto desempenho .

Depois de ter selecionado e criado um VM para a sua aplicação, terá de configurá-lo permitindo a InfiniBand. Para aprender a ativar o InfiniBand tanto no Windows como no Linux VMs, consulte [Enable InfiniBand](enable-infiniband.md).

Um componente crítico das cargas de trabalho do HPC é mpi. As séries HB e as séries HC suportam quase todos os tipos e versões de MPI. Para mais informações, consulte Configurar interface de [passagem de mensagens para HPC](setup-mpi.md).

Assim que escolhera a sua série VM, configurar a Infiniband e o MPI, está pronto para começar a construir as suas cargas de trabalho de HPC.

## <a name="next-steps"></a>Passos seguintes

- Reveja a visão geral da [série HB](hb-series-overview.md) e a visão geral da série [HC](hc-series-overview.md) para conhecer as principais diferenças e especificações.

- Para um nível mais elevado, vista arquitetónica da execução de cargas de trabalho hpc, consulte [Aputação de Alto Desempenho (HPC) no Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
