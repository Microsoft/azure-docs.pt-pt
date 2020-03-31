---
title: Série Ev3 e série Esv3 - Máquinas Virtuais Azure
description: Especificações para os VMs da série Ev3 e Esv3.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: aad4610a44c59ed95cf1ad9777329097886c5bed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164377"
---
# <a name="ev3-and-esv3-series"></a>Séries Ev3 e Esv3

A série Ev3 e Esv3 apresenta o processador Intel® Xeon® 8171M 2.1 GHz (Skylake) ou o Processador Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) numa configuração hiper-roscada, proporcionando uma proposta de melhor valor para a maioria das cargas de trabalho para fins gerais, e trazendo o Ev3 para a maioria das cargas de trabalho para fins gerais, e trazendo o Ev3 para a maioria das cargas de trabalho para fins gerais, e trazendo o Ev3 em alinhamento com o propósito geral VMs da maioria das outras nuvens.  A memória foi expandida (de 7 GiB/vCPU para 8 GiB/vCPU) enquanto os limites do disco e da rede foram ajustados numa base de núcleo para alinhar com a mudança para o hiperthreading. O Ev3 é o seguimento dos tamanhos vm de alta memória das famílias D/Dv2.

## <a name="ev3-series"></a>Série Ev3

As instâncias da série Ev3 baseiam-se na funcionalidade intel® Xeon® 8171M 2.1 GHz (Skylake) ou na Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) e intel Turbo Boost Technology 2.0. As instâncias da série Ev3 são ideais para aplicações empresariais com utilização intensiva da memória.

O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar os discos de armazenamento premium, utilize os tamanhos ESv3. Os medidores de preços e de faturação dos tamanhos ESv3 são os mesmos do que os da série Ev3.

A funcionalidade da Série Ev3 VM Intel® Hyper-Threading Technology.

ACU: 160 - 190

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração Ao Vivo: Apoiado

Atualizações de preservação da memória: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | NICs/Largura de banda da rede máximos |
|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8/10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |
| <sup>Standard_E64i_v3 1,2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

<sup>1</sup> Tamanhos de núcleo limitados disponíveis.

<sup>2</sup> A Instância está isolada em hardware dedicado a um único cliente.

## <a name="esv3-series"></a>Série Esv3

As instâncias da série Esv3 baseiam-se na funcionalidade intel® Xeon® 8171M 2.1 GHz (Skylake) ou no processador Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), Intel Turbo Boost Technology 2.0 e utiliza o armazenamento premium. Os casos da série Esv3 são ideais para aplicações empresariais intensivas de memória.

A funcionalidade da Série VM da Esv3, Intel® Hyper-Threading Technology.

ACU: 160-190

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração Ao Vivo: Apoiado

Atualizações de preservação da memória: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Entrada de armazenamento em cache e temperatura máxima: IOPS/MBps (tamanho cache em GiB) | Potência de disco max uncached: IOPS/MBps | Largura de banda de rede Max NICs/Esperado (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| <sup>Standard_E4s_v3 1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2/2000 |
| <sup>Standard_E8s_v3 1</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8/8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |
| <sup>Standard_E64is_v3 2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

<sup>1</sup> Tamanhos de núcleo limitados disponíveis.

<sup>2</sup> A Instância está isolada em hardware dedicado a um único cliente.

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
