---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008359"
---
Por enquanto, apenas discos ultra e SSDs premium podem ativar discos partilhados. Diferentes tamanhos de `maxShares` disco podem ter um limite `maxShares` diferente, que não pode exceder ao definir o valor. Para SSDs premium, os tamanhos do disco que suportam a partilha dos seus discos são P15 e maiores.

Para cada disco, pode `maxShares` definir um valor que representa o número máximo de nós que podem simultaneamente partilhar o disco. Por exemplo, se planeia criar um cluster de 2 nós, `maxShares=2`definiria . O valor máximo é um limite superior. Os nós podem juntar ou deixar o cluster (montar ou desmontar o disco) desde `maxShares` que o número de nós seja inferior ao valor especificado.

> [!NOTE]
> O `maxShares` valor só pode ser definido ou editado quando o disco é separado de todos os nós.

### <a name="premium-ssd-ranges"></a>Gamas Premium SSD

A tabela que se segue `maxShares` ilustra os valores máximos permitidos por tamanhos de disco premium:

|Tamanhos de disco  |limite maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Os limites de iopS e largura de `maxShares` banda para um disco não são afetados pelo valor. Por exemplo, o IOPS max de um disco P15 é de 1100, quer maxShares = 1 ou maxShares > 1.

### <a name="ultra-disk-ranges"></a>Gamas ultra-disco

O `maxShares` valor mínimo é 1, enquanto o valor máximo `maxShares` é de 5. Não existem restrições de tamanho em discos ultra, qualquer `maxShares`tamanho ultra disco pode usar qualquer valor para , até e incluindo o valor máximo.