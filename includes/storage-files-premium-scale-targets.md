---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "71180067"
---
#### <a name="additional-premium-file-share-level-limits"></a>Limites adicionais de nível de ações de ficheiros premium

|Área  |Destino  |
|---------|---------|
|Aumento/diminuição do tamanho mínimo    |1 GiB      |
|IOPS de linha de base    |1 IOPS por GiB, até 100.000|
|IOPS rebentando    |3x IOPS por GiB, até 100.000|
|Taxa de Egress         |60 MiB/s + 0,06 * GiB a provisionado        |
|Taxa de entrada| 40 MiB/s + 0,04 * GiB a provisionado |

#### <a name="file-level-limits"></a>Limites de nível de ficheiro

|Área  |Arquivo premium  |Arquivo padrão |
|---------|---------|---------|
|Tamanho                  |1 TiB         |1 TiB|
|Max IOPS por ficheiro     |5000         |1,000|
|Pegas simultâneas    |2.000         |2.000|
|Saída  |300 MiB/seg|      Ver valores de produção de ficheiros padrão|
|Entrada  |200 MiB/seg| Ver valores de produção de ficheiros padrão|
|Débito| Ver valores de entrada/saída de ficheiros premium| Até 60 MiB/seg|
