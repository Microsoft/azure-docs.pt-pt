---
title: Série Eav4 e série Easv4 - Máquinas Virtuais Azure
description: Especificações para os VMs da série Eav4 e Easv4.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: e05a2b97a25ce4cab7d619f4b62d405765cb08db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164394"
---
# <a name="eav4-and-easv4-series"></a>Séries Eav4 e Easv4

As séries Eav4 e Easv4 utilizam o processador EPYC<sup>7452</sup> da AMD numa configuração multi-roscada com cache L3 de até 256MB, aumentando as opções para executar a maioria das cargas de trabalho otimizadas pela memória. As séries Eav4 e Easv4 têm as mesmas configurações de memória e disco que o Ev3 & série Esv3.

## <a name="eav4-series"></a>Série Eav4

ACU: 230 - 260

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração Ao Vivo: Apoiado

Atualizações de preservação da memória: Suportado

Os tamanhos da série Eav4 baseiam-se no processador 2.35Ghz AMD EPYC<sup>TM</sup> 7452 que pode alcançar uma frequência máxima aumentada de 3,35GHz e utilizar sSD premium. Os tamanhos da série Eav4 são ideais para aplicações empresariais intensivas de memória. O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar o SSD premium, utilize os tamanhos da série Easv4. Os contadores de preços e faturação para os tamanhos Easv4 são os mesmos que as séries Eav3.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | Max NICs / Largura de banda de rede esperada (MBps) |
| -----|-----|-----|-----|-----|-----|-----|
| Padrão\_E2a\_v4|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| Padrão\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| Padrão\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| Padrão\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| Padrão\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8 / 10000 |
| Padrão\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16000 |
| Padrão\_E48a\_v4<sup>**</sup> |48|384|1200|32| | |
| Padrão\_E64a\_v4<sup>**</sup> |64|512|1600|32| | |
| Padrão\_E96a\_v4<sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>Estes tamanhos estão em Pré-visualização. Se estiver interessado em experimentar estes tamanhos [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)maiores, inscreva-se em .

## <a name="easv4-series"></a>Série Easv4

ACU: 230 - 260

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração Ao Vivo: Apoiado

Atualizações de preservação da memória: Suportado

Os tamanhos da série Easv4 baseiam-se no processador 2.35Ghz AMD EPYC<sup>TM</sup> 7452 que pode alcançar uma frequência máxima aumentada de 3,35GHz e utilizar sSD premium. Os tamanhos da série Easv4 são ideais para aplicações empresariais intensivas de memória.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Max NICs / Largura de banda de rede esperada (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)|32000 / 480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000 / 510 (800)|51200 / 768|8 / 16000 |
| Standard_E48as_v4<sup>**</sup> |48|384|768|32|  | |
| Standard_E64as_v4<sup>**</sup> |64|512|1024|32| | |
| Standard_E96as_v4<sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>Estes tamanhos estão em Pré-visualização. Se estiver interessado em experimentar estes tamanhos [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)maiores, inscreva-se em .

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
