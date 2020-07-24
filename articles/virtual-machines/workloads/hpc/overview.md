---
title: Computação de alto desempenho em VMs da série H - Azure Virtual Machines
description: Conheça as funcionalidades e capacidades dos VMs da série H otimizados para o HPC.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: 1f88496a6c474194089f20e128b2b92ec61e8559
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083434"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Computação de alto desempenho em VMs da série H

A computação de alto desempenho (HPC) nas séries HB e VMs da série HC permite o desempenho HPC mais otimizado de quaisquer VMs no Azure. Os VM otimizados do HPC são usados para resolver alguns dos problemas matemáticos mais difíceis, tais como: dinâmica de fluidos, simulações de petróleo e gás e modelação meteorológica.

Este artigo abrange algumas características-chave da série HB e VMs da série HC, por que estes VMs têm um bom desempenho em cenários de HPC, e como começar.

## <a name="features-and-capabilities"></a>Funcionalidades e capacidades

As séries HB e VMs da série HC são projetadas para proporcionar o melhor desempenho hpc, escalabilidade da interface de passagem de mensagens (MPI) e eficiência de custos para cargas de trabalho HPC.

### <a name="message-passing-interface"></a>Interface de passagem de mensagem

Série HB e série HC suportam quase todos os tipos e versões de MPI. Alguns dos tipos de MPI mais comuns e suportados são: OpenMPI, MVAPICH2, Plataforma MPI, Intel MPI, e todos os verbos de acesso remoto à memória direta (RDMA). Para obter mais informações, consulte [Configurar interface de passagem de mensagens para HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA e InfiniBand

A interface RDMA é padrão em VMs da série HB e da série HC. Casos capazes de RDMA comunicam através de uma rede InfiniBand, operando a taxas de dados melhoradas (EDR) para máquinas virtuais da série HB e da série HC. Casos com capacidade de RDMA podem aumentar a escalabilidade e desempenho de algumas aplicações de MPI.

A configuração InfiniBand que suporta as séries HB e VMs da série HC são árvores gordas que não bloqueiam com um design de baixo diâmetro para um desempenho RDMA consistente.

Consulte [Enable InfiniBand](enable-infiniband.md) para saber mais sobre a configuração da InfiniBand nas suas séries HB ou VMs da série HC.

## <a name="get-started"></a>Introdução

Primeiro, decida qual vm série H vai usar. Para obter detalhes sobre VMs otimizados hpc, consulte a [visão geral da série HB](hb-series-overview.md) e a [visão geral da série HC](hc-series-overview.md). Para especificações, consulte [os tamanhos VM de cálculo de alto desempenho](../../sizes-hpc.md).

Uma vez selecionado e criado um VM para a sua aplicação, terá de o configurar ativando a InfiniBand. Para aprender a ativar a InfiniBand em ambos os Windows e Linux VMs, consulte [Enable InfiniBand](enable-infiniband.md).

Um componente crítico das cargas de trabalho do HPC é o MPI. Série HB e série HC suportam quase todos os tipos e versões de MPI. Para obter mais informações, consulte [Configurar interface de passagem de mensagens para HPC](setup-mpi.md).

Assim que escolher a sua série VM, configurar a Infiniband e a MPI, está pronto para começar a construir as suas cargas de trabalho HPC.

## <a name="next-steps"></a>Passos seguintes

- Reveja a [visão geral](hb-series-overview.md) da série HB e [a visão geral da série HC](hc-series-overview.md) para saber sobre as principais diferenças e especificações.

- Para um nível mais elevado, vista arquitetónica da execução das cargas de trabalho do HPC, consulte [Azure .computação de alto desempenho (HPC).](/azure/architecture/topics/high-performance-computing/)
