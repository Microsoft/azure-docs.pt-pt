---
title: Série Mv2 - Azure Virtual Machines
description: Especificações para os VMs da série Mv2.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: 4576a8bdcb6077a9d5de20c52f99325fdcacc0f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84673703"
---
# <a name="mv2-series"></a>Série Mv2

A série Mv2 apresenta uma plataforma de alta potência e baixa latência em execução num processador Intel hiper-roscado® Xeon® Platinum 8180M 2.5GHz (Skylake) com uma frequência base de 2,5 GHz e uma frequência máxima turbo de 3,8 GHz. Todos os tamanhos de máquinas virtuais da série Mv2 podem usar discos padrão e persistentes premium. As instâncias da série Mv2 são tamanhos VM otimizados de memória que fornecem um desempenho computacional inigualável para suportar grandes bases de dados de memória e cargas de trabalho, com uma relação memória-CPU alta que é ideal para servidores de base de dados relacionais, caches grandes e análises na memória.

Recurso da série Mv2 da VM Intel® Tecnologia de Hiper-Rosca

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração ao vivo: Não suportado

Atualizações de preservação da memória: Não suportadas

Acelerador de escrita: [suportado](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Max NICs / Largura de banda esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> Mv2 série VMs são apenas geração 2 e suportam um subconjunto de imagens suportadas pela geração 2. Veja abaixo a lista completa de imagens suportadas para a série Mv2. Se estiver a utilizar o Linux, consulte [Suporte para a geração 2 VMs em Azure](./linux/generation-2.md) para obter instruções sobre como encontrar e selecionar uma imagem. Se estiver a utilizar o Windows, consulte [o Suporte para a geração 2 VMs no Azure](./windows/generation-2.md) para obter instruções sobre como encontrar e selecionar uma imagem. 

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

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
