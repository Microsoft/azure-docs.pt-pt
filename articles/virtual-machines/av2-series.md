---
title: Série Av2
description: Especificações para os VMs da série Av2.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 65aab240dd0be97aafa370883aa953f8eb766cf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650753"
---
# <a name="av2-series"></a>Série Av2

Os VMs da série Av2 podem ser implantados numa variedade de tipos e processadores de hardware. Os VMs da série Av2 têm configurações de desempenho e memória de CPU mais adequadas para cargas de trabalho de nível de entrada como desenvolvimento e teste. O tamanho é acelerado para oferecer um desempenho consistente do processador para a instância de execução, independentemente do hardware em que é implantado. Para determinar o hardware físico no qual este tamanho está implementado, consulte o hardware virtual a partir da Máquina Virtual. Alguns casos de uso de exemplo incluem servidores de desenvolvimento e teste, servidores web de baixo tráfego, bases de dados pequenas a médias, prova de conceitos e repositórios de código.

[ACU](acu.md): 100<br>
[Armazenamento Premium](premium-storage-performance.md): Não Suportado <br>
[Caching de armazenamento premium](premium-storage-performance.md): Não suportado <br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado <br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado <br>
[Suporte de geração VM](generation-2.md): Geração 1 <br>
<br>

| Tamanho | vCore | Memória: GiB | Armazenamento (SSD) temporário GiB | Produção de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados/produção de dados máximos: IOPS | NICs máximos | Largura de banda de rede esperada (Mbps)
|---|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2 | 250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços : [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos : [tipos de discos](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
