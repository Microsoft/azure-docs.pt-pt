---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81008359"
---
Por enquanto, apenas discos ultra e SSDs premium podem ativar discos partilhados. Diferentes tamanhos de disco podem ter um `maxShares` limite diferente, que não pode exceder ao definir o `maxShares` valor. Para SSDs premium, os tamanhos de disco que suportam a partilha dos seus discos são P15 e maiores.

Para cada disco, pode definir um `maxShares` valor que represente o número máximo de nós que podem simultaneamente partilhar o disco. Por exemplo, se planeia configurar um cluster de falha de 2 nós, definiria `maxShares=2` . O valor máximo é um limite superior. Os nós podem unir-se ou sair do cluster (montar ou desmontar o disco) desde que o número de nós seja inferior ao `maxShares` valor especificado.

> [!NOTE]
> O `maxShares` valor só pode ser definido ou editado quando o disco é desligado de todos os nós.

### <a name="premium-ssd-ranges"></a>Gamas Premium SSD

O quadro a seguir ilustra os valores máximos permitidos `maxShares` por tamanhos de disco premium:

|Tamanhos de disco  |limite maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Os limites de largura de banda e IOPS para um disco não são afetados pelo `maxShares` valor. Por exemplo, o IOPS máximo de um disco P15 é de 1100 quer maxShares = 1 ou maxShares > 1.

### <a name="ultra-disk-ranges"></a>Gamas ultra disco

O valor mínimo `maxShares` é 1, enquanto o valor máximo `maxShares` é de 5. Não existem restrições de tamanho em discos ultra, qualquer disco ultra de tamanho pode usar qualquer valor para `maxShares` , até e incluindo o valor máximo.