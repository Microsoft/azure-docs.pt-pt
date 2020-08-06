---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: c2bd10ab4c98fe2e77332c3cc2566ab2f0c7ad42
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87841971"
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
|Tamanho                  |4 TiB         |1 TiB|
|Max IOPS por ficheiro     |5000         |1,000|
|Pegas simultâneas    |2.000         |2.000|
|Saída  |300 MiB/seg|      Ver valores de produção de ficheiros padrão|
|Entrada  |200 MiB/seg| Ver valores de produção de ficheiros padrão|
|Débito| Ver valores de entrada/saída de ficheiros premium| Até 60 MiB/seg|
