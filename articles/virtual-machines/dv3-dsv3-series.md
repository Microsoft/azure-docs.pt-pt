---
title: Série Dv3 e Dsv3 - Máquinas Virtuais Azure
description: Especificações para os VMs da série Dv3 e Dsv3.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ea5811fa20ea4866655de74d79ff3905ba03f16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164411"
---
# <a name="dv3-and-dsv3-series"></a>Séries Dv3 e Dsv3

A série Dv3 funciona nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) numa configuração hiper-roscada, proporcionando uma proposta de melhor valor para a maioria das cargas de trabalho gerais. A memória foi expandida (de ~3.5 GiB/vCPU para 4 GiB/vCPU) enquanto os limites do disco e da rede foram ajustados numa base de núcleo para alinhar com o movimento para o hiperthreading. A série Dv3 já não tem os tamanhos VM de alta memória da série D/Dv2, estes foram movidos para a memória otimizada [ev3 e série Esv3.](ev3-esv3-series.md)

Exemplo, os casos de utilização da série D incluem aplicações de nível empresarial, bases de dados relacionais, caches na memória e análise.

## <a name="dv3-series"></a>Série Dv3

Os tamanhos da série Dv3 funcionam nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com a Intel Boost Technology 2.0. Os tamanhos da série Dv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar os discos de armazenamento premium, utilize os tamanhos Dsv3. Os medidores de preços e de faturação dos tamanhos Dsv3 são os mesmos do que os da série Dv3.

Os VMs da série Dv3 apresentam a Intel® Tecnologia Hyper-Threading.

ACU: 160-190

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração Ao Vivo: Apoiado

Atualizações de preservação da memória: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Entrada de armazenamento de temperatura máxima: IOPS/Ler MBps/Write MBps | Largura de banda Max NICs/Rede |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Série Dsv3

Os tamanhos da série Dsv3 funcionam nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com intel Turbo Boost Technology 2.0 e usam armazenamento premium. Os tamanhos da série Dsv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

Os VMs da série Dsv3 apresentam intel® tecnologia hyper-threading.

ACU: 160-190

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração Ao Vivo: Apoiado

Atualizações de preservação da memória: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Entrada de armazenamento em cache e temperatura máxima: IOPS/MBps (tamanho cache em GiB) | Potência de disco max uncached: IOPS/MBps | Largura de banda de rede Max NICs/Esperado (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 3200/48    | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 6400/96    | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 12800/192  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 25600/384  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

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
