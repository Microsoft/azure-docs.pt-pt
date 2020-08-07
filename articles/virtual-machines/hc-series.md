---
title: Série HC - Azure Virtual Machines
description: Especificações para os VMs da série HC.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: b73051ab7cc122f1f371ddac9c77e7243bfa0d73
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923548"
---
# <a name="hc-series"></a>Série HC

Os VMs da série HC são otimizados para aplicações impulsionadas por computação densa, tais como análise implícita de elementos finitos, dinâmica molecular e química computacional. Os VMS HC dispõem de 44 núcleos de processador Intel Xeon Platinum 8168, 8 GB de RAM por núcleo CPU e sem hiperligação. A plataforma Intel Xeon Platinum suporta o rico ecossistema de ferramentas de software da Intel, como a Biblioteca Intel Math Kernel.

ACU: 297-315

Armazenamento Premium: Suportado

Caching de armazenamento premium: suportado

Migração ao vivo: Não suportado

Atualizações de preservação da memória: Não suportadas

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência base cpu (GHz) | Frequência all-cores (GHz, pico) | Frequência monomússária (GHz, pico) | Desempenho de RDMA (Gb/s) | Suporte mpi | Armazenamento temporário (GB) | Discos de dados máximos | Max Ethernet NICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Tudo | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a configuração dos seus VMs,](./workloads/hpc/configure.md) [permitindo a InfiniBand,](./workloads/hpc/enable-infiniband.md) [configurando MPI,](./workloads/hpc/setup-mpi.md)e otimizando as aplicações de HPC para Azure na [HPC Workloads](./workloads/hpc/overview.md).
- Leia sobre os últimos anúncios e alguns exemplos e resultados do HPC no [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma visão arquitetónica de alto nível da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
- Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
