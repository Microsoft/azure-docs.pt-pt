---
title: Série H - Azure Virtual Machines
description: Especificações para os VMs da série H.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: b1f30e91b9ce96daf8b2eb8ac6c8cb38b86b347f
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89594412"
---
# <a name="h-series"></a>Série H

Os VMs da série H são otimizados para aplicações impulsionadas por altas frequências de CPU ou por grandes necessidades de memória por requisitos fundamentais. Os VMs da série H apresentam núcleos de processador Intel Xeon E5 2667 v3 da série H, até 14 GB de RAM por núcleo cpu, e sem hiperfinaling. A série H apresenta 56 Gb/seg Mellanox FDR InfiniBand numa configuração de árvore de gordura não bloqueada para um desempenho RDMA consistente. Os VMs da série H não estão ativados atualmente e suportam a Intel MPI 5.x e MS-MPI.

ACU: 290-300

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração ao vivo: Não suportado

Atualizações de preservação da memória: Não suportadas

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência base cpu (GHz) | Frequência all-cores (GHz, pico) | Frequência monomússária (GHz, pico) | Desempenho de RDMA (Gb/s) | Suporte mpi | Armazenamento temporário (GB) | Discos de dados máximos | Débito máximo do disco: IOPS | Max Ethernet NICs |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> Para aplicações MPI, a rede de backend dedicada RDMA é ativada pela rede FDR InfiniBand.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

> [!NOTE]
> Entre os [VMs capazes de RDMA,](sizes-hpc.md#rdma-capable-instances)a série H não está ativada. Por conseguinte, os [VM Images suportados,](./workloads/hpc/configure.md#vm-images)os requisitos do [controlador InfiniBand](./workloads/hpc/enable-infiniband.md) e [as bibliotecas MPI](./workloads/hpc/setup-mpi.md) suportadas são diferentes dos VMs ativados pela SR-IOV.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [a configuração dos seus VMs,](./workloads/hpc/configure.md) [permitindo a InfiniBand,](./workloads/hpc/enable-infiniband.md) [configurando MPI](./workloads/hpc/setup-mpi.md) e otimizando as aplicações HPC para Azure na [HPC Workloads](./workloads/hpc/overview.md).
- Leia sobre os últimos anúncios e alguns exemplos e resultados do HPC no [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
- Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
