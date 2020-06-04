---
title: Série Edv4 e Edsv4 - Azure Virtual Machines
description: Especificações para os VMs da série Ev4, Edv4, Esv4 e Edsv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: d6f70e00bc33430cba13895b6d0c9a0aac5ef246
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84338690"
---
# <a name="edv4-and-edsv4-series"></a>Séries Edv4 e Edsv4

A série Edv4 e Edsv4 funciona nos processadores Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) numa configuração hiper roscada, e são ideais para várias aplicações empresariais intensivas de memória e apresentam até 504 GiB de RAM, [Intel Turbo Boost Technology &reg; 2.0,](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) [Tecnologia de &reg; Hiper-Threading Intel](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e [ &reg; Extensões Avançadas de Vetores Intel 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Estes novos tamanhos VM terão 50% maior armazenamento local, bem como melhores IOPS de disco local tanto para ler como escrever em comparação com os tamanhos [Ev3/Esv3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) com [VMs Gen2](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)

## <a name="edv4-series"></a>Série Edv4

Os tamanhos da série Edv4 são executados nos processadores Intel &reg; Xeon &reg; Platinum 8272CL (Lago de Cascade). Os tamanhos de máquina virtual Edv4 dispõem de até 504 GiB de RAM, além de armazenamento SSD local rápido e grande (até 2.400 GiB). Estas máquinas virtuais são ideais para aplicações e aplicações empresariais intensivas de memória que beneficiam de baixa latência, armazenamento local de alta velocidade. Pode anexar SSDs standard e armazenamento de hdds standard aos Edv4 VMs. 

ACU: 195 - 210

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração ao Vivo: Suportado

Atualizações de preservação da memória: Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps | Largura de banda max NICs/Rede Esperada (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2/1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2/2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4/4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8/8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8/10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8/16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8/24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8/30000 |


## <a name="edsv4-series"></a>Série Edsv4

Os tamanhos da série Edsv4 são executados nos processadores Intel &reg; Xeon &reg; Platinum 8272CL (Lago de Cascade). Os tamanhos de máquina virtual Edsv4 dispõem de até 504 GiB de RAM, além de armazenamento SSD local rápido e grande (até 2.400 GiB). Estas máquinas virtuais são ideais para aplicações e aplicações empresariais intensivas de memória que beneficiam de baixa latência, armazenamento local de alta velocidade.

ACU: 195-210

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração ao Vivo: Suportado

Atualizações de preservação da memória: Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps | Produção de disco não-abacatado por maxilar: IOPS/MBps | Largura de banda max NICs/Rede Esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120(50) | 3200/48 | 2/1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242(100) | 6400/96 | 2/2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485(200) | 12800/192 | 4/4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968(400) | 25600/384 | 8/8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211(500)  | 32000/480  | 8/10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936(800) | 51200/768  | 8/16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8/24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8/30000 |

<sup>1</sup> [Tamanhos de núcleo limitados disponíveis)](https://docs.microsoft.com/azure/virtual-machines/windows/constrained-vcpu).


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
