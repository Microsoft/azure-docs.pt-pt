---
title: Série Mv2 - Máquinas Virtuais Azure
description: Especificações para os VMs da série Mv2.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 04/07/2020
ms.author: lahugh
ms.openlocfilehash: 764dc93608ae3b8882b7048a722c6d3415cbc644
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885791"
---
# <a name="mv2-series"></a>Série Mv2

A série Mv2 apresenta alta potência, plataforma de latência baixa que funciona num processador Intel hiper-roscado® Xeon® Platinum 8180M 2.5GHz (Skylake) com uma frequência base de 2,5 GHz e uma frequência máxima turbo de 3,8 GHz. Todos os tamanhos de máquinas virtuais da série Mv2 podem usar discos persistentes padrão e premium. Os casos da série Mv2 são tamanhos VM otimizados de memória que fornecem um desempenho computacional inigualável para suportar grandes bases de dados e cargas de trabalho na memória, com uma elevada relação memória-CPU que é ideal para servidores de base de dados relacionais, grandes caches e análise sintetizada.

Funcionalidade da Série MV2 VM Intel® Tecnologia Hiper-Threading

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração Ao Vivo: Não Suportado

Atualizações de preservação da memória: não suportadas

Acelerador de escrita: [Suportado](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Max NICs / Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> VMs série Mv2 são apenas de geração 2. Se estiver a usar o Linux, consulte suporte para os [VMs da geração 2 no Azure](./linux/generation-2.md) para obter instruções sobre como encontrar e selecionar uma imagem. Se estiver a utilizar o Windows, consulte o [Suporte para VMs da geração 2 no Azure](./windows/generation-2.md) para obter instruções sobre como encontrar e selecionar uma imagem. Libertações mínimas do sistema operativo necessárias para todos os quatro tipos vM da série Mv2 diferentes, como:

- Windows Server 2019 ou mais tarde
- SUSE Linux Enterprise Server 12 SP4 e mais tarde ou SUSE Linux Enterprise Server 15 SP1 e mais tarde
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 ou mais tarde 
- Oracle Enterprise Linux 7.7 ou mais tarde



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
