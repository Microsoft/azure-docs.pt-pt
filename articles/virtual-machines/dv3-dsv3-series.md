---
title: Séries Dv3 e Dsv3
description: Especificações para os VMs da série Dv3 e Dsv3.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: jushiman
ms.openlocfilehash: 3d0735b99de3e0c68b48747d69e4720a9cbb3416
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500704"
---
# <a name="dv3-and-dsv3-series"></a>Séries Dv3 e Dsv3

A série Dv3 é executada em Intel® Xeon® Platinum 8272CL (Lago de Cascade), Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2 673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) numa configuração hiper roscada, proporcionando uma proposta de melhor valor para a maioria das cargas de trabalho para fins gerais. A memória foi expandida (de ~3.5 GiB/vCPU para 4 GiB/vCPU) enquanto os limites de disco e rede foram ajustados numa base por núcleo para alinhar com a mudança para a hipertonagem. A série Dv3 já não tem os tamanhos VM de alta memória da série D/Dv2, estas foram movidas para a memória otimizada [série Ev3 e Esv3](ev3-esv3-series.md).

Os casos de utilização da série D incluem aplicações de nível empresarial, bases de dados relacionais, caching na memória e análise.

## <a name="dv3-series"></a>Série Dv3

Os tamanhos da série Dv3 funcionam em Intel® Xeon® Platinum 8272CL (Lago cascade), Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com [intel Turbo Boost Technology &reg; 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html). Os tamanhos da série Dv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar os discos de armazenamento premium, utilize os tamanhos Dsv3. Os medidores de preços e de faturação dos tamanhos Dsv3 são os mesmos do que os da série Dv3.

Os VMs da série Dv3 apresentam Tecnologia ® Hyper-Threading Intel.

[ACU](acu.md): 160-190<br>
[Armazenamento Premium](premium-storage-performance.md): Não Suportado<br>
[Caching de armazenamento premium](premium-storage-performance.md): Não suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1<br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Largura de banda Max NICs/Rede |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Série Dsv3

Os tamanhos da série Dsv3 são executados em Intel® Xeon® Platinum 8272CL (Lago cascata), Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E E 5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com [Intel Turbo Boost Technology &reg; 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) e usam armazenamento premium. Os tamanhos da série Dsv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

Os VMs da série Dsv3 apresentam Tecnologia intel® Hyper-Threading.

[ACU](acu.md): 160-190<br>
[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps (tamanho da cache em GiB) | Produção de armazenamento em cache de rajada máxima e temporária: IOPS/MBps<sup>1</sup> | Produção de disco não-abacatado por maxilar: IOPS/MBps | Saída de disco sem rutura de max: IOPS/MBps<sup>1</sup> | Largura de banda de rede Max NICs/Expect (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 4000/100    |3200/48    | 4000/100   | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 8000/200    |6400/96    | 8000/200   | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 16000/400   |12800/192  | 16000/400  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 32000/800   |25600/384  | 32000/800  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 64000/1600  |51200/768  | 64000/1600 | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 96000/2000  |76800/1152 | 80000/2000 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 128000/2000 |80000/1200 | 80000/2000 | 8/30000 |

<sup>1</sup>  VMs série Dsv3 pode [rebentar](./disk-bursting.md) o seu desempenho em disco e chegar ao seu máximo de explosão por até 30 minutos de cada vez.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)
- [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/)
- Para obter mais informações sobre os tipos de discos, consulte [que tipos de disco estão disponíveis no Azure?](disks-types.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.