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
ms.openlocfilehash: 6e35e32c92535a408c8df22d7306895150a59519
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050153"
---
# <a name="ev4-and-esv4-series"></a>Séries Ev4 e Esv4

A série Ev4 e Esv4 funciona nos processadores Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) numa configuração hiper roscada, são ideais para várias aplicações empresariais intensivas de memória e apresentam até 504GiB de RAM. Apresenta uma velocidade do relógio Turbo de 3,4 GHz.

> [!NOTE]
> Para perguntas frequentes, consulte os  [tamanhos Azure VM sem disco temporário local](azure-vms-no-temp-disk.md).

## <a name="ev4-series"></a>Série Ev4

Os tamanhos da série Ev4 são executados no Intel Xeon &reg; Platinum 8272CL (Lago cascade). As instâncias da série Ev4 são ideais para aplicações empresariais intensivas em memória. Os VMs da série Ev4 apresentam tecnologia de &reg; hiper rosca Intel.

O armazenamento remoto do disco de dados é faturado separadamente das máquinas virtuais. Para utilizar discos de armazenamento premium, utilize os tamanhos Esv4. Os preços e os contadores de faturação para tamanhos Esv4 são os mesmos que a série Ev4.

> [!IMPORTANT]
> Estes novos tamanhos estão atualmente em Visualização Pública Apenas. Pode inscrever-se nestas séries Ev4 e Esv4 [aqui.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u) 

ACU: 195 - 210

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração ao Vivo: Suportado

Atualizações de preservação da memória: Suportadas

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

Os tamanhos da série Esv4 são executados no Intel &reg; Xeon &reg; Platinum 8272CL (Lago cascata). As instâncias da série Esv4 são ideais para aplicações empresariais intensivas em memória. Os VMs da série Evs4 apresentam tecnologia de &reg; hiper rosca Intel. O armazenamento remoto do disco de dados é faturado separadamente das máquinas virtuais.

> [!IMPORTANT]
> Estes novos tamanhos estão atualmente em Visualização Pública Apenas. Pode inscrever-se nestas séries Ev4 e Esv4 [aqui.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u) 

ACU: 195-210

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração ao Vivo: Suportado

Atualizações de preservação da memória: Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção em cache máximo: IOPS/MBps (tamanho da cache em GiB) | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos|Largura de banda esperada da rede (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Apenas armazenamento remoto | 4 | 19000/120 (50) | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Apenas armazenamento remoto | 8 | 38500/242 (100) | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Apenas armazenamento remoto | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Apenas armazenamento remoto | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | Apenas armazenamento remoto | 32 | 193000/1211 (500) | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Apenas armazenamento remoto | 32 | 308000/1936 (800) | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Apenas armazenamento remoto | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Apenas armazenamento remoto | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

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

Mais informações sobre tipos de discos : [tipos de discos](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
