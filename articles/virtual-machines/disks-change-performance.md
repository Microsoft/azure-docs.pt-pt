---
title: Níveis de desempenho para discos geridos pela Azure
description: Saiba mais sobre os níveis de desempenho para discos geridos.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/19/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 28980756ac9e41c9477d687ea9df608b512759e3
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94986788"
---
# <a name="performance-tiers-for-managed-disks"></a>Níveis de desempenho para discos geridos

O desempenho do seu disco gerido Azure é definido quando cria o seu disco, sob a forma do seu nível de desempenho. O nível de desempenho determina o IOPS e o desempenho que o seu disco gerido tem. Quando define o tamanho do seu disco, é selecionado automaticamente um nível de desempenho. O nível de desempenho pode ser alterado na implementação ou posterior, sem alterar o tamanho do disco.

Mudar o nível de desempenho permite-lhe preparar-se e satisfazer uma maior procura sem utilizar a capacidade de rebentamento do disco. Pode ser mais rentável mudar o seu nível de desempenho em vez de depender de rebentar, dependendo do tempo necessário para o desempenho adicional. Isto é ideal para eventos que temporariamente requerem um nível de desempenho consistentemente mais elevado, como compras de férias, testes de desempenho ou execução de um ambiente de treino. Para lidar com estes eventos, pode utilizar um nível de desempenho mais elevado durante o tempo que precisar. Em seguida, pode voltar ao nível original quando já não necessitar do desempenho adicional.

## <a name="how-it-works"></a>Como funciona

Quando implementa ou disponibiliza um disco pela primeira vez, o nível de desempenho de base para esse disco é definido com base no tamanho do disco provisionado. Você pode usar um nível de desempenho superior ao original para satisfazer uma procura mais elevada. Quando já não precisares desse nível de desempenho, podes voltar ao nível de desempenho inicial da linha de base.

A sua faturação muda à medida que o seu nível de desempenho muda. Por exemplo, se forre um disco P10 (128 GiB), o seu nível de desempenho de base é definido como P10 (500 IOPS e 100 MBps). Será cobrado à taxa P10. Pode atualizar o nível para corresponder ao desempenho de P50 (7.500 IOPS e 250 MBps) sem aumentar o tamanho do disco. Durante o upgrade, será cobrado à taxa P50. Quando já não precisares de um desempenho superior, podes voltar ao nível P10. O disco será novamente cobrado à taxa P10.

| Tamanho do disco | Nível de desempenho de base | Pode ser atualizado para |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Nenhum |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | Nenhum |

Para obter informações sobre faturação, consulte [preços de disco geridos](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>Próximos passos

Para aprender a alterar o seu nível de desempenho, consulte [o portal](disks-performance-tiers-portal.md) ou os artigos [PowerShell/CLI.](disks-performance-tiers.md)

