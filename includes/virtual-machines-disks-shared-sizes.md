---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471713"
---
Por enquanto, só os SSDs premium podem ativar discos partilhados. Os tamanhos do disco que suportam esta funcionalidade são P15 e maiores. Diferentes tamanhos de `maxShares` disco podem ter um limite `maxShares` diferente, que não pode exceder ao definir o valor.

Para cada disco, pode `maxShares` definir um valor que representa o número máximo de nós que podem simultaneamente partilhar o disco. Por exemplo, se planeia criar um cluster de 2 nós, `maxShares=2`definiria . O valor máximo é um limite superior. Os nós podem juntar ou deixar o cluster (montar ou desmontar o disco) desde `maxShares` que o número de nós seja inferior ao valor especificado.

> [!NOTE]
> O `maxShares` valor só pode ser definido ou editado quando o disco é separado de todos os nós.

A tabela que se segue `maxShares` ilustra os valores máximos permitidos pelo tamanho do disco:

|Tamanhos do disco  |limite maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Os limites de iopS e largura de `maxShares` banda para um disco não são afetados pelo valor. Por exemplo, os IOPS max de um disco P15 são 1100, quer maxShares = 1 ou maxShares > 1.