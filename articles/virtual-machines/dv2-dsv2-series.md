---
title: Série Dv2 e Dsv2 - Azure Virtual Machines
description: Especificações para os VMs da série Dv2 e Dsv2.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 6120d7b63a948b0de004a6a0d731c0d075689da1
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678227"
---
# <a name="dv2-and-dsv2-series"></a>Séries Dv2 e DSv2

A série Dv2 e Dsv2, uma continuação da série D original, apresenta um CPU mais potente e uma configuração de CPU-para-memória ideal, tornando-as adequadas para a maioria das cargas de trabalho de produção. A série Dv2 é cerca de 35% mais rápida que a série D. A série Dv2 corre no Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2...2.000 3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com o Intel Turbo Boost Technology 2.0. A série Dv2 tem as mesmas configurações de memória e disco da série D.

## <a name="dv2-series"></a>Série Dv2

Os tamanhos da série Dv2 são executados no Intel® Xeon® 8171M 2.1GHz (Skylake) ou no Intel® Xeon® E5-2673 v 4 2.3 GHz (Broadwell) ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com intel Turbo Boost Technology 2.0.

ACU: 210-250

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração ao Vivo: Suportado

Atualizações de preservação da memória: Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Produção de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados máximos | Produção: IOPS | Largura de banda de rede Max NICs/Expect (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3.5 | 50  | 3000/46/23    | 4  | 4x500  | 2/750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8x500  | 2/1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16x500 | 4/3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32x500 | 8/6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8/12000 |

## <a name="dsv2-series"></a>Série DSv2

Os tamanhos da série DSv2 são executados no Intel® Xeon® 8171M 2.1GHz (Skylake) ou no Intel® Xeon® E5-2673 v4 4 0 2.3 GHz (Broadwell) ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com Intel Turbo Boost Technology 2.0 e utilizam armazenamento premium.

ACU: 210-250

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração ao Vivo: Suportado

Atualizações de preservação da memória: Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps (tamanho da cache em GiB) | Produção de disco não-abacatado por maxilar: IOPS/MBps | Largura de banda de rede Max NICs/Expect (Mbps) |
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

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
