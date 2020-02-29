---
title: Série HBv2 - Máquinas Virtuais Azure
description: Especificações para os VMs da série HBv2.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164802"
---
# <a name="hbv2-series"></a>Série HBv2

Os VMs da série HBv2 são otimizados para aplicações impulsionadas pela largura de banda da memória, tais como dinâmica de fluidos, análise de elementos finitos e simulação do reservatório. Os VMs HBv2 apresentam 120 núcleos de processador AMD EPYC 7742, 4 GB de RAM por núcleo CPU e sem multithreading simultâneo. Cada HBv2 VM fornece até 340 GB/sede de largura de banda de memória, e até 4 teraFLOPS de computação FP64.

Armazenamento Premium: Suportado

Migração Ao Vivo: Não Suportado

Atualizações de preservação da memória: não suportadas

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho RDMA (Gb/s) | Suporte do MPI | Armazenamento temporário (GB) | Discos de dados máximos | Max Ethernet NICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | Todos | 480 + 960 | 8 | 1 |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.