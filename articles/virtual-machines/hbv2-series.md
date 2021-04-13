---
title: Série HBv2 - Azure Virtual Machines
description: Especificações para os VMs da série HBv2.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 25f0933c2d0b8b3c8ec227ce52c974a50a671043
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309735"
---
# <a name="hbv2-series"></a>Série HBv2

Os VMs da série HBv2 são otimizados para aplicações que são impulsionadas pela largura de banda da memória, tais como dinâmica de fluidos, análise de elementos finitos e simulação do reservatório. Os VMs HBv2 dispõem de 120 núcleos de processador AMD EPYC 7742, 4 GB de RAM por núcleo CPU e sem multi-leitura simultânea. Cada HBv2 VM fornece até 340 GB/seg de largura de banda de memória, e até 4 teraFLOPS de FP64 compute.

Os VMs da série HBv2 apresentam 200 Gb/seg Mellanox HDR InfiniBand. Estes VMs estão ligados numa árvore de gordura não bloqueada para um desempenho RDMA otimizado e consistente. Estes VMs suportam o Encaminhamento Adaptativo e o Transporte Conectado Dinâmico (DCT, além dos transportes RC e UD padrão). Estas funcionalidades aumentam o desempenho da aplicação, escalabilidade e consistência, e a sua utilização é recomendada.

[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Ultra Discos](disks-types.md#ultra-disk): Suportado[(Saiba mais](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) sobre disponibilidade, utilização e desempenho) <br>
[Migração ao Vivo](maintenance-and-updates.md): Não Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Não suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
[Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Suportado[(Saiba mais](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) sobre desempenho e questões potenciais) <br>
[Discos EFÉMEROS :](ephemeral-os-disks.md)Não Suportados <br>
<br>

| Tamanho | vCPU | Processador | Memória (GiB) | Largura de banda de memória GB/s | Frequência base cpu (GHz) | Frequência all-cores (GHz, pico) | Frequência monomússária (GHz, pico) | Desempenho de RDMA (Gb/s) | Suporte mpi | Armazenamento temporário (GiB) | Discos de dados máximos | Max Ethernet vNICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 456 | 350 | 2.45 | 3,1 | 3.3 | 200 | Todos | 480 + 960 | 8 | 8 |

Saiba mais sobre:
- [Arquitetura e topologia VM](./workloads/hpc/hbv2-series-overview.md)
- Pilha de [software](./workloads/hpc/hbv2-series-overview.md#software-specifications) suportado, incluindo SISTEMA suportado
- Desempenho [](./workloads/hpc/hbv2-performance.md) esperado do VM da série HBv2

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Otimizada para GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

- Leia sobre os últimos anúncios, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs comunitários Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
- Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
