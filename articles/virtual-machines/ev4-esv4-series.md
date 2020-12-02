---
title: Série Ev4 e Esv4 - Azure Virtual Machines
description: Especificações para os VMs da série Ev4 e Esv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 6d8ec3633fec79fc4fdc04986c8b94c671ae9d1a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460322"
---
# <a name="ev4-and-esv4-series"></a>Séries Ev4 e Esv4

A série Ev4 e Esv4 funciona nos processadores Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) numa configuração hiper roscada, são ideais para várias aplicações empresariais intensivas de memória e apresentam até 504GiB de RAM. Apresenta uma velocidade do relógio Turbo de 3,4 GHz.

> [!NOTE]
> Para perguntas frequentes, consulte os  [tamanhos Azure VM sem disco temporário local](azure-vms-no-temp-disk.md).

## <a name="ev4-series"></a>Série Ev4

Os tamanhos da série Ev4 são executados no Intel Xeon &reg; Platinum 8272CL (Lago cascade). As instâncias da série Ev4 são ideais para aplicações empresariais intensivas em memória. Os VMs da série Ev4 apresentam Tecnologia &reg; intel Hyper-Threading.

O armazenamento remoto do disco de dados é faturado separadamente das máquinas virtuais. Para utilizar discos de armazenamento premium, utilize os tamanhos Esv4. Os preços e os contadores de faturação para tamanhos Esv4 são os mesmos que a série Ev4.

[ACU](acu.md): 195 - 210<br>
[Armazenamento Premium](premium-storage-performance.md): Não Suportado<br>
[Caching de armazenamento premium](premium-storage-performance.md): Não suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1<br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | NICs máximos|Largura de banda esperada da rede (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | Apenas armazenamento remoto | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | Apenas armazenamento remoto | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | Apenas armazenamento remoto | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | Apenas armazenamento remoto | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | Apenas armazenamento remoto | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | Apenas armazenamento remoto | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | Apenas armazenamento remoto | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | Apenas armazenamento remoto | 32| 8|30000 |


## <a name="esv4-series"></a>Série Esv4

Os tamanhos da série Esv4 são executados no Intel &reg; Xeon &reg; Platinum 8272CL (Lago cascata). As instâncias da série Esv4 são ideais para aplicações empresariais intensivas em memória. Os VMs da série Evs4 apresentam Tecnologia &reg; intel Hyper-Threading. O armazenamento remoto do disco de dados é faturado separadamente das máquinas virtuais.

[ACU](acu.md): 195-210<br>
[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos|Largura de banda esperada da rede (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Apenas armazenamento remoto | 4 | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Apenas armazenamento remoto | 8 | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Apenas armazenamento remoto | 16 | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Apenas armazenamento remoto | 32 | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | Apenas armazenamento remoto | 32 | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Apenas armazenamento remoto | 32 | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Apenas armazenamento remoto | 32 | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Apenas armazenamento remoto | 32 | 80000/1200 | 8|30000 |
| Standard_E80is_v4 <sup>1,2</sup> | 80 | 504 | Apenas armazenamento remoto | 32 | 80000/1200 | 8|30000 |

<sup>1</sup> [Tamanhos de núcleo limitados disponíveis)](./constrained-vcpu.md).

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

Mais informações sobre tipos de discos : [tipos de discos](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
