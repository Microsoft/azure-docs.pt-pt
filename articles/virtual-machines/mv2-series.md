---
title: Série Mv2 - Azure Virtual Machines
description: Especificações para os VMs da série Mv2.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: b15fdc3826a72e9cfb039b6b2994179ab9565404
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562525"
---
# <a name="mv2-series"></a>Série Mv2

A série Mv2 apresenta uma plataforma de alta potência e baixa latência em execução num processador Intel hiper-roscado® Xeon® Platinum 8180M 2.5GHz (Skylake) com uma frequência base de 2,5 GHz e uma frequência máxima turbo de 3,8 GHz. Todos os tamanhos de máquinas virtuais da série Mv2 podem usar discos padrão e persistentes premium. As instâncias da série Mv2 são tamanhos VM otimizados de memória que fornecem um desempenho computacional inigualável para suportar grandes bases de dados de memória e cargas de trabalho, com uma relação memória-CPU alta que é ideal para servidores de base de dados relacionais, caches grandes e análises na memória.

Recurso da série Mv2 da VM Intel® Hyper-Threading Tecnologia

[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração ao Vivo](maintenance-and-updates.md): Não Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Não suportado<br>
[Suporte vm geração](generation-2.md): Geração 2<br>
[Write Accelerator](./how-to-enable-write-accelerator.md): Suportado<br>
[Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Suportado<br>
[Discos EFÉMEROS :](ephemeral-os-disks.md)Não Suportados <br>
<br>

|Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs máximos | Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 | 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 | 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 | 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 | 32000 |

<sup>1</sup> Mv2 série VMs são apenas geração 2 e suportam um subconjunto de imagens suportadas pela geração 2. Veja abaixo a lista completa de imagens suportadas para a série Mv2. Se estiver a utilizar o Linux, consulte [Suporte para a geração 2 VMs em Azure](./generation-2.md) para obter instruções sobre como encontrar e selecionar uma imagem. Se estiver a utilizar o Windows, consulte [o Suporte para a geração 2 VMs no Azure](./generation-2.md) para obter instruções sobre como encontrar e selecionar uma imagem. 

- Windows Server 2019 ou mais tarde
- SUSE Linux Enterprise Server 12 SP4 e mais tarde ou SUSE Linux Enterprise Server 15 SP1 e mais tarde
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 ou mais tarde 
- Oracle Enterprise Linux 7.7 ou mais tarde



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Otimizada para GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos : [tipos de discos](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
