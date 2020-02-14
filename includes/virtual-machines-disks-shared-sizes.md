---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9000cab026ec08bd78d8a8f334c8967a8a747c95
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202297"
---
Por enquanto, só os SSDs premium podem ativar discos partilhados. Os tamanhos do disco que suportam esta funcionalidade são P15 e maiores. Diferentes tamanhos de disco podem ter um limite de `maxShares` diferente, que não pode exceder ao definir o valor `maxShares`.

Para cada disco, pode definir um valor `maxShares` que representa o número máximo de nós que podem simultaneamente partilhar o disco. Por exemplo, se planeia criar um cluster de 2 nós, definirá `maxShares=2`. O valor máximo é um limite superior. Os nós podem juntar ou deixar o cluster (montar ou desmontar o disco) desde que o número de nós seja inferior ao valor de `maxShares` especificado.

> [!NOTE]
> O valor `maxShares` só pode ser definido ou editado quando o disco é separado de todos os nós.

A tabela que se segue ilustra os valores máximos permitidos para `maxShares` pelo tamanho do disco:

|Tamanhos do disco  |limite maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Os limites de iopS e largura de banda para um disco não são afetados pelo valor `maxShares`. Por exemplo, os IOPS max de um disco P15 são 1100, quer maxShares = 1 ou maxShares > 1.