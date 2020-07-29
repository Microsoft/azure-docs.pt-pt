---
title: Série Ev3 e série Esv3
description: Especificações para os VMs da série Ev3 e Esv3.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 334c8128ab7869d38700668b6f46a6b2a6f96d28
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87269364"
---
# <a name="ev3-and-esv3-series"></a>Séries Ev3 e Esv3

A série Ev3 e Esv3 é executada em Intel® Xeon® Platinum 8272CL (Cascade Lake), ou Intel® Xeon® 8171M 2.1 GHz (Skylake), ou o processador Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) numa configuração hiper roscada, proporcionando uma proposta de melhor valor para a maioria das cargas de trabalho para fins gerais, e colocando o Ev3 em alinhamento com o propósito geral VMs da maioria das outras nuvens.  A memória foi expandida (de 7 GiB/vCPU para 8 GiB/vCPU) enquanto os limites de disco e rede foram ajustados numa base por núcleo para alinhar com a mudança para a hipertonagem. O Ev3 é o seguimento dos tamanhos VM de alta memória das famílias D/Dv2.

## <a name="ev3-series"></a>Série Ev3

As instâncias da série Ev3 são executadas na Intel® Xeon® Platinum 8272CL (Lago cascade), Intel® Xeon® 8171M 2.1 GHz (Skylake), ou nos processadores Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), e recurso Intel Turbo Boost Technology 2.0. As instâncias da série Ev3 são ideais para aplicações empresariais com utilização intensiva da memória.

O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar os discos de armazenamento premium, utilize os tamanhos ESv3. Os medidores de preços e de faturação dos tamanhos ESv3 são os mesmos do que os da série Ev3.

Ev3 série VM's feature Intel® Hyper-Threading Technology.

ACU: 160 - 190

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração ao Vivo: Suportado

Atualizações de preservação da memória: Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | NICs máximos | Largura de banda de rede |
|---|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2|1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2|2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4|4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8|8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8|10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8|16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8|24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8|30000 |
| Standard_E64i_v3 <sup>1,2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8|30000 |

<sup>1</sup> Tamanhos de núcleo limitados disponíveis.

<sup>2</sup> A instância é isolada para hardware dedicado a um único cliente.

## <a name="esv3-series"></a>Série Esv3

As instâncias da série Esv3 são executadas na Intel® Xeon® Platinum 8272CL (Lago cascade), Intel® Xeon® 8171M 2.1 GHz (Skylake), ou no processador Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), recurso Intel Turbo Boost Technology 2.0 e use armazenamento premium. As instâncias da série Esv3 são ideais para aplicações empresariais intensivas em memória.

Esv3 série VM's feature Intel® Hyper-Threading Technology.

ACU: 160-190

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração ao Vivo: Suportado

Atualizações de preservação da memória: Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps (tamanho da cache em GiB) | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2|2000 |
| Standard_E8s_v3 <sup>1</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4|4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8|8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8|24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8|30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8|30000 |

<sup>1</sup> Tamanhos de núcleo limitados disponíveis.

<sup>2</sup> A instância é isolada para hardware dedicado a um único cliente.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos : [tipos de discos](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types#ultra-ssd-preview/)


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
