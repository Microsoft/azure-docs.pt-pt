---
title: Série Eav4 e série Easv4
description: Especificações para os VMs da série Eav4 e Easv4.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 9b6ea9491575236a3ef84d660ead8022d13e01ef
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964133"
---
# <a name="eav4-and-easv4-series"></a>Séries Eav4 e Easv4

A série Eav4 e a série Easv4 utilizam o processador EPYC<sup>TM</sup> 7452 da AMD 2.35Ghz numa configuração multi-roscada com até 256MB de cache L3, aumentando as opções para executar a maioria das cargas de trabalho otimizadas pela memória. As séries Eav4 e Easv4 têm as mesmas configurações de memória e disco que a série Ev3 & Esv3.

## <a name="eav4-series"></a>Série Eav4

[ACU](acu.md): 230 - 260<br>
[Armazenamento Premium](premium-storage-performance.md): Não Suportado<br>
[Caching de armazenamento premium](premium-storage-performance.md): Não suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Gerações 1 e 2<br>
<br>

Os tamanhos da série Eav4 são baseados no processador AMD EPYC<sup>TM</sup> 7452 de 2,35Ghz que pode alcançar uma frequência máxima aumentada de 3,35GHz. Os tamanhos da série Eav4 são ideais para aplicações empresariais intensivas em memória. O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar o SSD premium, utilize os tamanhos da série Easv4. Os preços e os contadores de faturação para os tamanhos Easv4 são os mesmos que a série Eav3.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | NICs máximos | Largura de banda de rede esperada (Mbps) |
| -----|-----|-----|-----|-----|-----|-----|-----|
| Standard \_ E2a \_ v4|2|16|50|4|3000 / 46 / 23|2 | 1000 |
| Standard \_ E4a \_ v4|4|32|100|8|6000 / 93 / 46|2 | 2000 |
| Standard \_ E8a \_ v4|8|64|200|16|12000 / 187 / 93|4 | 4000 |
| Standard \_ E16a \_ v4|16|128|400|32|24000 / 375 / 187|8 | 8000 |
| Standard \_ E20a \_ v4|20|160|500|32|30000 / 468 / 234|8 | 10000 |
| Standard \_ E32a \_ v4|32|256|800|32|48000 / 750 / 375|8 | 16000 |
| Standard \_ E48a \_ v4|48|384|1200|32|96000 / 1000 (500)|8 | 24000 |
| Standard \_ E64a \_ v4|64|512|1600|32|96000 / 1000 (500)|8 | 30000 |
| Standard \_ E96a \_ v4|96|672|2400|32|96000 / 1000 (500)|8 | 30000 |

## <a name="easv4-series"></a>Série Easv4

[ACU](acu.md): 230 - 260<br>
[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Gerações 1 e 2<br>
<br>

Os tamanhos da série Easv4 baseiam-se no processador 2.35Ghz AMD EPYC<sup>TM</sup> 7452 que pode alcançar uma frequência máxima aumentada de 3,35GHz e utilizar SSD premium. Os tamanhos da série Easv4 são ideais para aplicações empresariais intensivas em memória.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs máximos | Largura de banda de rede esperada (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000 / 32 (50)|3200 / 48|2 | 1000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2 | 2000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4 | 4000 |
| Standard_E16as_v4|16|128|256|32|32000 / 255 (400)|25600 / 384|8 | 8000 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)|32000 / 480|8 | 10000 |
| Standard_E32as_v4|32|256|512|32|64000 / 510 (800)|51200 / 768|8 | 16000 |
| Standard_E48as_v4|48|384|768|32|96000 / 1020 (1200)|76800 / 1148|8 | 24000 |
| Standard_E64as_v4|64|512|1024|32|128000 / 1020 (1600)|80000 / 1200|8 | 30000 |
| Standard_E96as_v4 <sup>1</sup>|96|672|1344|32|192000 / 1020 (2400)|80000 / 1200|8 | 30000 |

<sup>1</sup> [Tamanhos de núcleo limitados disponíveis](./constrained-vcpu.md).

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos: [tipos de discos](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
