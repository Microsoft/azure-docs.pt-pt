---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "71180067"
---
#### <a name="additional-premium-file-share-level-limits"></a>Limites adicionais de nível de partilha de ficheiros premium

|Área  |Destino  |
|---------|---------|
|Aumento/diminuição do tamanho mínimo    |1 GiB      |
|IOPS de base    |1 IOPS por GiB, até 100.000|
|IOPS rebentando    |3x IOPS por GiB, até 100.000|
|Taxa de egress         |60 MiB/s + 0,06 * giB provisionado        |
|Taxa de ingresso| 40 MiB/s + 0,04 * giB provisionado |

#### <a name="file-level-limits"></a>Limites de nível de ficheiro

|Área  |Arquivo premium  |Arquivo padrão |
|---------|---------|---------|
|Tamanho                  |1 TiB         |1 TiB|
|IOPS max por ficheiro     |5000         |1,000|
|Pegas simultâneas    |2.000         |2.000|
|Saída  |300 MiB/seg|      Ver valores padrão de entrada de ficheiros|
|Entrada  |200 MiB/seg| Ver valores padrão de entrada de ficheiros|
|Débito| Ver valores de entrada/saída de ficheiros premium| Até 60 MiB/seg|
