---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180067"
---
#### <a name="additional-premium-file-share-level-limits"></a>Limites de nível de compartilhamento de arquivo Premium adicionais

|Área  |Destino  |
|---------|---------|
|Aumento/diminuição de tamanho mínimo    |1 GiB      |
|IOPS de linha de base    |1 IOPS por GiB, até 100.000|
|Intermitência de IOPS    |3x IOPS por GiB, até 100.000|
|Taxa de egresso         |60 MiB/s + 0, 6 * GiB provisionados        |
|Taxa de entrada| 40 MiB/s + 0, 4 * GiB provisionados |

#### <a name="file-level-limits"></a>Limites de nível de arquivo

|Área  |Arquivo Premium  |Arquivo padrão |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|IOPS máxima por arquivo     |5,000         |1,000|
|Identificadores simultâneos    |2\.000         |2\.000|
|Saída  |300 MiB/s|      Consulte valores de taxa de transferência de arquivo padrão|
|Entrada  |200 MiB/s| Consulte valores de taxa de transferência de arquivo padrão|
|Débito| Consulte valores de entrada/saída do arquivo Premium| Até 60 MiB/s|
