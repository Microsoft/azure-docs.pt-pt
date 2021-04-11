---
title: Computação de alto desempenho na InfiniBand habilitada série H e VMs da série N - Azure Virtual Machines
description: Conheça as funcionalidades e capacidades da InfiniBand ativada série H e VMs da série N otimizados para hpc.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 03/18/2021
ms.reviewer: cynthn
ms.openlocfilehash: 65b37a8c07e083f5e9809812e2d4446cc48717d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720598"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>Computação de alto desempenho na InfiniBand habilitada série H e VMs da série N

Os VMs da Série H e VM da série N da Azure são projetados para fornecer desempenho de classe de liderança, escalabilidade de Interface de Passagem de Mensagens (MPI) e eficiência de custos para uma variedade de cargas de trabalho de HPC e IA no mundo real. Estes VM otimizados de computação de alto desempenho (HPC) são usados para resolver alguns dos problemas mais computacionalmente intensivos na ciência e engenharia, tais como: dinâmica de fluidos, modelação de terra, simulações meteorológicas, etc.

Estes artigos descrevem como começar com as séries H e VMs da série N ativadas pela InfiniBand em Azure, bem como a configuração ideal das cargas de trabalho de HPC e IA nos VMs para escalabilidade.

## <a name="features-and-capabilities"></a>Funcionalidades e capacidades

Os VMs da série H e da série N ativados infiniband são projetados para proporcionar o melhor desempenho HPC, escalabilidade de MPI e eficiência de custos para cargas de trabalho HPC. Consulte [as séries H](../../sizes-hpc.md) e [VMs da série N](../../sizes-gpu.md) para saber mais sobre as funcionalidades e capacidades dos VMs.

### <a name="rdma-and-infiniband"></a>RDMA e InfiniBand

As [séries H](../../sizes-hpc.md) e [VMs da série N](../../sizes-gpu.md) com capacidade [RDMA](../../sizes-hpc.md#rdma-capable-instances) comunicam-se sobre a baixa latência e a rede InfiniBand de alta largura de banda. A capacidade de RDMA sobre tal interligação é fundamental para aumentar a escalabilidade e desempenho das cargas de trabalho de HPC e IA distribuídas. Os VMs da série H e vMs da série H ativados infiniband estão ligados numa árvore de gordura que não bloqueia com um design de baixo diâmetro para um desempenho RDMA otimizado e consistente.
Consulte [Enable InfiniBand](enable-infiniband.md) para saber mais sobre a criação de InfiniBand nos VMs ativados pela InfiniBand.

### <a name="message-passing-interface"></a>Interface de passagem de mensagem

O SR-IOV permitiu que a série H e a série N suportassem quase todas as bibliotecas e versões mpi. Algumas das bibliotecas de MPI mais utilizadas são: Intel MPI, OpenMPI, HPC-X, MVAPICH2, MPICH, Plataforma MPI. Todos os verbos de acesso à memória direta remota (RDMA) são suportados.
Consulte [configurar o MPI](setup-mpi.md) para saber mais sobre a instalação de várias bibliotecas de MPI suportadas e a sua configuração ideal.

## <a name="get-started"></a>Introdução

O primeiro passo é selecionar o tipo VM da [série H](../../sizes-hpc.md) e [n-série](../../sizes-gpu.md) VM ideal para a carga de trabalho com base nas especificações VM e [na capacidade de RDMA](../../sizes-hpc.md#rdma-capable-instances).
Em segundo lugar, configurar o VM, permitindo a InfiniBand. Existem vários métodos para o fazer, incluindo a utilização de imagens de VM otimizadas com condutores assados; consulte [a Otimização para Linux](configure.md) e [Enable InfiniBand](enable-infiniband.md) para mais detalhes.
Em terceiro lugar, para cargas de trabalho distribuídas de nó, escolher e configurar mpi adequadamente é fundamental. Consulte [o MPI](setup-mpi.md) de configuração para mais detalhes.
Em quarto lugar, para o desempenho e escalabilidade, configuram da melhor forma as cargas de trabalho seguindo orientações específicas para a família VM, tais como para a [visão geral da série HBv3](hbv3-series-overview.md) e [visão geral da série HC](hc-series-overview.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba [como configurar e otimizar](configure.md) os VMs da [série H](../../sizes-hpc.md) e [da série N.](../../sizes-gpu.md)
- Reveja a visão geral da [série HBv3](hb-series-overview.md) e [a visão geral da série HC](hc-series-overview.md) para aprender sobre a configuração ótima das cargas de trabalho para desempenho e escalabilidade.
- Leia sobre os últimos anúncios, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs comunitários Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
