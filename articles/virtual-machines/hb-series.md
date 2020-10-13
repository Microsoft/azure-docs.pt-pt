---
title: Série HB
description: Especificações para os VMs da série HB.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 3e79373da3de2750a47e68a6d8f10c3ffb9c38de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652980"
---
# <a name="hb-series"></a>Série HB

Os VMs da série HB são otimizados para aplicações impulsionadas pela largura de banda da memória, tais como dinâmica de fluidos, análise explícita de elementos finitos e modelação meteorológica. Os VMs HB dispõem de 60 núcleos de processador AMD EPYC 7551, 4 GB de RAM por núcleo CPU e nenhuma multi-leitura simultânea. Um HB VM fornece até 260 GB/seg de largura de banda de memória.

Os VMs da série HB apresentam 100 Gb/sec Mellanox EDR InfiniBand. Estes VMs estão ligados numa árvore de gordura não bloqueada para um desempenho RDMA otimizado e consistente. Estes VMs suportam o Encaminhamento Adaptativo e o Transporte Conectado Dinâmico (DCT, adicional aos transportes RC e UD padrão). Estas funcionalidades aumentam o desempenho da aplicação, escalabilidade e consistência, e a sua utilização é fortemente recomendada.

[ACU](acu.md): 199-216<br>
[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração ao Vivo](maintenance-and-updates.md): Não Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Não suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
<br>

| Tamanho | vCPU | Processador | Memória (GiB) | Largura de banda de memória GB/s | Frequência base cpu (GHz) | Frequência all-cores (GHz, pico) | Frequência monomússária (GHz, pico) | Desempenho de RDMA (Gb/s) | Suporte mpi | Armazenamento temporário (GiB) | Discos de dados máximos | Max Ethernet NICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Todos | 700 | 4 | 1 |

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
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
- Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
