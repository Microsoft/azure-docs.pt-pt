---
title: Série Dv2 e Dsv2 - Máquinas Virtuais Azure
description: Especificações para os VMs da série Dv2 e Dsv2.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 808b14f118e842cb9e52d110075f92ba25a343c9
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164428"
---
# <a name="dv2-and-dsv2-series"></a>Séries Dv2 e DSv2

As séries Dv2 e Dsv2, uma continuação da série D original, apresentam um CPU mais potente e uma configuração ideal cpu-a-memória tornando-as adequadas para a maioria das cargas de trabalho de produção. A série Dv2 é cerca de 35% mais rápida que a série D. As séries Dv2 correm nos intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com os processadores Intel Turbo Boost Technology 2.0. A série Dv2 tem as mesmas configurações de memória e disco da série D.

## <a name="dv2-series"></a>Série Dv2

Os tamanhos da série Dv2 funcionam nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake) ou nos processadores Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) ou na Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com intel Turbo Boost Technology 2.0.

ACU: 210-250

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração Ao Vivo: Apoiado

Atualizações de preservação da memória: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Entrada de armazenamento de temperatura máxima: IOPS/Ler MBps/Write MBps | Discos de dados máximos | Entrada: IOPS | Largura de banda de rede Max NICs/Esperado (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3.5 | 50  | 3000/46/23    | 4  | 4x500  | 2/750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8x500  | 2/1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16x500 | 4/3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32x500 | 8/6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8/12000 |

## <a name="dsv2-series"></a>Série DSv2

Os tamanhos da série DSv2 funcionam nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake) ou nos processadores Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) ou na Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com a Intel Turbo Boost Technology 2.0 e o armazenamento premium.

ACU: 210-250

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração Ao Vivo: Apoiado

Atualizações de preservação da memória: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Entrada de armazenamento em cache e temperatura máxima: IOPS/MBps (tamanho cache em GiB) | Potência de disco max uncached: IOPS/MBps | Largura de banda de rede Max NICs/Esperado (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3.5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2/750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2/1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4/3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8/6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8/12000 |

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
