---
title: Computação em VMs de série H - máquinas virtuais do Azure de alto desempenho | Documentos da Microsoft
description: Saiba mais sobre as funcionalidades e capacidades de VMs de série H otimizadas para HPC.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.author: amverma
ms.openlocfilehash: d6e857a87e4c7df8ffb2be1eefb7a0290da5b10a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800065"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Computação em VMs de série H de alto desempenho

Elevado desempenho computação (HPC) em VMs de série HC e de série HB ativar o desempenho de HPC mais otimizado de VMs no Azure. HPC com otimização de VMs são utilizados para resolver alguns dos problemas matemáticos mais difíceis, como: dinâmicas de fluidos, simulações de petróleo e gás e modelos de previsão meteorológica.

Este artigo aborda algumas das principais funcionalidades de série HB e VMs de série HC, por que estas VMs executam bem em cenários HPC e como começar a utilizar.

## <a name="features-and-capabilities"></a>Funcionalidades e capacidades

Série HB VMs da série HC são concebidas para proporcionar o melhor desempenho de HPC, escalabilidade de interface (MPI), de transmissão de mensagens e otimizar custos eficiência para cargas de trabalho HPC.

### <a name="message-passing-interface"></a>Interface de passagem de mensagens

As séries HB e HC suportam quase todas as versões e tipos MPI. Algumas das mais as tipos MPI comuns, suportadas são: OpenMPI, MVAPICH2, plataforma MPI, a MPI Intel e toda a memória direta remota verbos (RDMA) de acesso. Para obter mais informações, consulte [configurar a Message Passing Interface para HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>InfiniBand e RDMA

A interface RDMA é padrão em série HB e as VMs da série HC. Instâncias com capacidade RDMA comunicam através de uma rede InfiniBand, operacional às taxas de dados (EDR) para máquinas de virtuais de séries de HB e HC. Instâncias com capacidade RDMA podem aumentar a escalabilidade e desempenho de alguns aplicativos MPI.

A configuração de InfiniBand HB série e as VMs da série HC de suporte são sem bloqueio fat árvores com um design de diâmetro de baixa para um desempenho consistente RDMA.

Ver [InfiniBand ativar](enable-infiniband.md) para saber mais sobre como configurar InfiniBand no seu HB série ou VMs da série HC.

## <a name="get-started"></a>Introdução

Em primeiro lugar, decida qual VM da série H pretende usar. Para obter detalhes sobre o HPC otimizado VMs, veja [descrição geral da série HB](hb-series-overview.md) e [descrição geral da série HC](hc-series-overview.md). Para especificações, consulte [tamanhos de VM de computação de alto desempenho](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

Depois de selecionada e criou uma VM para a aplicação, terá de configurá-lo ao ativar InfiniBand. Para saber como ativar InfiniBand do Windows e VMs do Linux, veja [InfiniBand ativar](enable-infiniband.md).

Um componente crítico de cargas de trabalho HPC é MPI. As séries HB e HC suportam quase todas as versões e tipos MPI. Para obter mais informações, consulte [configurar a Message Passing Interface para HPC](setup-mpi.md).

Depois de escolher sua série VM, configurar Infiniband e MPI, está pronto para começar a criar as suas cargas de trabalho HPC.

## <a name="next-steps"></a>Passos seguintes

- Reveja os [descrição geral da série HB](hb-series-overview.md) e [descrição geral da série HC](hc-series-overview.md) para saber mais sobre as principais diferenças e as especificações.

- Para um nível mais elevado, a visão arquitetural da execução de cargas de trabalho HPC, consulte [alto desempenho HPC (computação) no Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
