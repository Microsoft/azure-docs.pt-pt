---
title: Séries Dav4 e Dasv4
description: Especificações para os VMs da série Dav4 e Dasv4.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 6ab532e3d824aa857a35bf2898aea2efba28df71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653031"
---
# <a name="dav4-and-dasv4-series"></a>Séries Dav4 e Dasv4

As séries Dav4 e Dasv4 são novos tamanhos utilizando o processador EPYC TM 7452 da AMD 2.35Ghz<sup>EPYC TM</sup> 7452 numa configuração multi-roscada com até 256 MB de cache L3 dedicando 8 MB dessa cache L3 a cada 8 núcleos aumentando as opções de clientes para executar as suas cargas de trabalho para fins gerais. As séries Dav4 e Dasv4 têm as mesmas configurações de memória e disco que a série D & Dsv3.

## <a name="dav4-series"></a>Série Dav4

[ACU](acu.md): 230-260<br>
[Armazenamento Premium](premium-storage-performance.md): Não Suportado<br>
[Caching de armazenamento premium](premium-storage-performance.md): Não suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1<br>
<br>

Os tamanhos da série Dav4 são baseados no processador AMD EPYC<sup>TM</sup> 7452 de 2.35Ghz que pode alcançar uma frequência máxima aumentada de 3,35GHz. Os tamanhos da série Dav4 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção. O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar SSD premium, utilize os tamanhos Dasv4. Os preços e os contadores de faturação para tamanhos Dasv4 são os mesmos que a série Dav4.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | NICs máximos | Largura de banda de rede esperada (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 | 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 | 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 | 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 | 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 | 16000 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000 / 1000 / 500 | 8 | 24000 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96000 / 1000 / 500 | 8 | 30000 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96000 / 1000 / 500 | 8 | 30000 |

## <a name="dasv4-series"></a>Série Dasv4

[ACU](acu.md): 230-260<br>
[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
<br>

Os tamanhos da série Dasv4 baseiam-se no processador 2.35Ghz AMD EPYC<sup>TM</sup> 7452 que pode alcançar uma frequência máxima aumentada de 3,35GHz e utilizar SSD premium. Os tamanhos da série Dasv4 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs máximos | Largura de banda de rede esperada (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000 / 32 (50)|3200 / 48|2 | 1000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)|6400 / 96|2 | 2000 |
| Standard_D8as_v4|8|32|64|16|16000 / 128 (200)|12800 / 192|4 | 4000 |
| Standard_D16as_v4|16|64|128|32|32000 / 255 (400)|25600 / 384|8 | 8000 |
| Standard_D32as_v4|32|128|256|32|64000 / 510 (800)|51200 / 768|8 | 16000 |
| Standard_D48as_v4|48|192|384|32|96000 / 1020 (1200)|76800 / 1148|8 | 24000 |
| Standard_D64as_v4|64|256|512|32|128000 / 1020 (1600)|80000 / 1200|8 | 30000 | 
| Standard_D96as_v4|96|384|768|32|192000 / 1020 (2400)|80000 / 1200|8 | 30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos : [tipos de discos](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.