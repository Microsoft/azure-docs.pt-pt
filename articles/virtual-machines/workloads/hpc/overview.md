---
title: Computação de alto desempenho na InfiniBand habilitada série H e VMs da série N - Azure Virtual Machines
description: Conheça as funcionalidades e capacidades da InfiniBand ativada série H e VMs da série N otimizados para hpc.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms-subservice: hpc
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/29/2020
ms.openlocfilehash: e8bff2b08782dcced63b5eb35f1baac310a1d3ca
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87431933"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>Computação de alto desempenho na InfiniBand habilitada série H e VMs da série N

Os VMs da Série H e VM da série N da Azure são projetados para fornecer desempenho de classe de liderança, escalabilidade de Interface de Passagem de Mensagens (MPI) e eficiência de custos para uma variedade de cargas de trabalho de HPC e IA no mundo real. Estes VM otimizados de computação de alto desempenho (HPC) são usados para resolver alguns dos problemas mais computacionalmente intensivos na ciência e engenharia, tais como: dinâmica de fluidos, modelação de terra, simulações meteorológicas, etc.

Estes artigos descrevem como começar com as séries H e VMs da série N ativadas pela InfiniBand em Azure, bem como a configuração ideal das cargas de trabalho de HPC e IA nos VMs para escalabilidade.

## <a name="features-and-capabilities"></a>Funcionalidades e capacidades

Os VMs da série H e da série N ativados infiniband são projetados para proporcionar o melhor desempenho HPC, escalabilidade de MPI e eficiência de custos para cargas de trabalho HPC. Consulte [as séries H](../../sizes-hpc.md) e [VMs da série N](../../sizes-gpu.md) para saber mais sobre as funcionalidades e capacidades dos VMs.

### <a name="rdma-and-infiniband"></a>RDMA e InfiniBand

As séries H e VMs da série N com capacidade de RDMA comunicam-se sobre a baixa latência e a rede InfiniBand de alta largura de banda. A capacidade de RDMA sobre tal interligação é fundamental para aumentar a escalabilidade e desempenho das cargas de trabalho de HPC e IA distribuídas. Os VMs da série H e vMs da série H ativados infiniband estão ligados numa árvore de gordura que não bloqueia com um design de baixo diâmetro para um desempenho RDMA otimizado e consistente.
Consulte [VMs capazes de RDMA](../../sizes-hpc.md#rdma-capable-instances) para saber mais sobre a capacidade de RDMA de tais VMs e sua matriz de suporte - sistemas operativos suportados, instalação do controlador InfiniBand, suporte MPI etc. Consulte [Enable InfiniBand](enable-infiniband.md) para saber mais sobre a criação de InfiniBand nos VMs ativados pela InfiniBand.

### <a name="message-passing-interface"></a>Interface de passagem de mensagem

O SR-IOV permitiu que a série H e a série N suportassem quase todas as bibliotecas e versões mpi. Algumas das bibliotecas mpi mais comuns e suportadas são: Intel MPI, OpenMPI, MPICH, MVAPICH2, Plataforma MPI, e todos os verbos de acesso remoto à memória direta (RDMA).
Consulte [configurar o MPI](setup-mpi.md) para saber mais sobre a instalação de várias bibliotecas de MPI suportadas e a sua configuração ideal.

## <a name="get-started"></a>Introdução

O primeiro passo é selecionar o VM da [série H](../../sizes-hpc.md) e da série N ideal para a carga [de](../../sizes-gpu.md) trabalho com base nas especificações VM e na capacidade [de RDMA](../../sizes-hpc.md#rdma-capable-instances).
Em segundo lugar, configurar o VM, permitindo a InfiniBand. Existem vários métodos para o fazer, incluindo a utilização de imagens de VM otimizadas com condutores assados; consulte [a Otimização para Linux](configure.md) e [Enable InfiniBand](enable-infiniband.md) para mais detalhes.
Em terceiro lugar, para cargas de trabalho distribuídas de nó, escolher e configurar o MPI é fundamental. Consulte [o MPI](setup-mpi.md) de configuração para mais detalhes.
Em quarto lugar, para o desempenho e escalabilidade, configuram da melhor forma as cargas de trabalho seguindo orientações específicas para a família VM, tais como para a [visão geral da série HB](hb-series-overview.md) e para a [visão geral da série HC](hc-series-overview.md).

## <a name="next-steps"></a>Próximos passos

- Reveja a [visão geral](hb-series-overview.md) da série HB e [a visão geral da série HC](hc-series-overview.md) para aprender sobre a configuração ideal das cargas de trabalho para desempenho e escalabilidade.
- Leia sobre os últimos anúncios e alguns exemplos e resultados do HPC no [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
