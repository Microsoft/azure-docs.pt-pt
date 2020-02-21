---
title: HC-series - Máquinas Virtuais Azure
description: Especificações para os VMs da série HC.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 10c26c738fe51f6e557491475afd0545d3f04ab3
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493231"
---
# <a name="hc-series"></a>Série HC

Os VMs da série HC são otimizados para aplicações impulsionadas por uma computação densa, tais como análise implícita de elementos finitos, dinâmica molecular e química computacional. Os HC VMs apresentam 44 núcleos de processador Intel Xeon Platinum 8168, 8 GB de RAM por núcleo CPU, e sem hiperthreading. A plataforma Intel Xeon Platinum suporta o rico ecossistema de ferramentas de software da Intel, como a Intel Math Kernel Library.

ACU: 297-315

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho RDMA (Gb/s) | Suporte do MPI | Armazenamento temporário (GB) | Discos de dados máximos | Max Ethernet NICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Todos | 700 | 4 | 1 |

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