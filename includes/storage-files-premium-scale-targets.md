---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: d704c6026e9d007a7365a3b72649ca509585da4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88057727"
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

|Área  |Arquivo padrão  |Arquivo premium  |
|---------|---------|---------|
|Tamanho     |1 TiB         |4 TiB         |
|Max IOPS por ficheiro      |1,000         |5000         |
|Pegas simultâneas     |2.000         |2.000         |
|Saída     |Ver valores de produção de ficheiros padrão         |300 MiB/seg         |
|Entrada     |Ver valores de produção de ficheiros padrão         |200 MiB/seg         |
|Débito     |Até 60 MiB/seg         |Ver valores de entrada/saída de ficheiros premium         |