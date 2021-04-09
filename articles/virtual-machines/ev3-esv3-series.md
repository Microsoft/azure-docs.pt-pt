---
title: Série Ev3 e série Esv3
description: Especificações para os VMs da série Ev3 e Esv3.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: mimckitt
ms.openlocfilehash: 878ca249a02d3b53d0085052b2ff1caf590e3ce3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557867"
---
# <a name="ev3-and-esv3-series"></a>Séries Ev3 e Esv3

A série Ev3 e Esv3 é executada em Intel® Xeon® Platinum 8272CL (Cascade Lake), ou Intel® Xeon® 8171M 2.1 GHz (Skylake), ou o processador Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) numa configuração hiper roscada, proporcionando uma proposta de melhor valor para a maioria das cargas de trabalho para fins gerais, e colocando o Ev3 em alinhamento com o propósito geral VMs da maioria das outras nuvens.  A memória foi expandida (de 7 GiB/vCPU para 8 GiB/vCPU) enquanto os limites de disco e rede foram ajustados numa base por núcleo para alinhar com a mudança para a hipertonagem. O Ev3 é o seguimento dos tamanhos VM de alta memória das famílias D/Dv2.

## <a name="ev3-series"></a>Série Ev3

As instâncias da série Ev3 são executadas na Intel® Xeon® Platinum 8272CL (Lago cascade), Intel® Xeon® 8171M 2.1 GHz (Skylake), ou nos processadores Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), e recurso Intel Turbo Boost Technology 2.0. As instâncias da série Ev3 são ideais para aplicações empresariais com utilização intensiva da memória.

O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar os discos de armazenamento premium, utilize os tamanhos ESv3. Os medidores de preços e de faturação dos tamanhos ESv3 são os mesmos do que os da série Ev3.

Ev3 série VM's feature Intel® Hyper-Threading Technology.

[ACU](acu.md): 160 - 190<br>
[Armazenamento Premium](premium-storage-performance.md): Não Suportado<br>
[Caching de armazenamento premium](premium-storage-performance.md): Não suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1<br>
[Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Suportado *(Requer um mínimo de 4 vCPU*)<br>
[Discos EFÉMEROS :](ephemeral-os-disks.md)Não Suportados <br>
<br>

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
| Standard_E64i_v3 <sup>1,2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

<sup>1</sup> Tamanhos de núcleo limitados disponíveis.

<sup>2</sup> A instância é isolada para hardware dedicado a um único cliente.

## <a name="esv3-series"></a>Série Esv3

As instâncias da série Esv3 são executadas na Intel® Xeon® Platinum 8272CL (Lago cascade), Intel® Xeon® 8171M 2.1 GHz (Skylake), ou no processador Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), recurso Intel Turbo Boost Technology 2.0 e use armazenamento premium. As instâncias da série Esv3 são ideais para aplicações empresariais intensivas em memória.

Esv3 série VM's feature Intel® Hyper-Threading Technology.

[ACU](acu.md): 160-190<br>
[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
[Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Suportado *(Requer um mínimo de 4 vCPU*)<br>
[Discos EFÉMEROS](ephemeral-os-disks.md): Suportados <br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps (tamanho da cache em GiB) | Rebentamento de armazenamento em cache e temporário: IOPS/MBps<sup>3</sup> | Produção de disco não-abacatado por maxilar: IOPS/MBps |  Saída do disco rebentado: IOPS/MBps<sup>3</sup>| Largura de banda de rede Max NICs/Expect (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v3                | 2  | 16  | 32  | 4  | 4000/32 (50)       | 4000/100    | 3200/48    | 4000/100 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>   | 4  | 32  | 64  | 8  | 8000/64 (100)      | 8000/200    | 6400/96    | 8000/200 | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>   | 8  | 64  | 128 | 16 | 16000/128 (200)    | 16000/400   | 12800/192  | 16000/400 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup>  | 16 | 128 | 256 | 32 | 32000/256 (400)    | 32000/800   | 25600/384  | 32000/800 | 8/8000 |
| Standard_E20s_v3               | 20 | 160 | 320 | 32 | 40000/320 (400)    | 40000/1000  | 32000/480  | 40000/1000 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 64000/1600  | 51200/768  | 64000/1600 | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 96000/2000  | 76800/1152 | 80000/2000 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |

<sup>1</sup> Tamanhos de núcleo limitados disponíveis.

<sup>2</sup> A instância é isolada para hardware dedicado a um único cliente.

<sup>3</sup> VMs da série Esv3 podem [rebentar o](./disk-bursting.md) seu desempenho em disco e chegar ao seu máximo de explosão por até 30 minutos de cada vez.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Otimizada para GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)
- [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/)
- Para obter mais informações sobre os tipos de discos, consulte [que tipos de disco estão disponíveis no Azure?](disks-types.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.