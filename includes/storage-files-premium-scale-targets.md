---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: 368f08272173b019873dfe20e1164d6baf72ff5e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542670"
---
#### <a name="additional-premium-file-share-level-limits"></a>Limites de nível de partilha de ficheiros de premium adicionais

|Área  |Destino  |
|---------|---------|
|Tamanho mínimo aumentar/diminuir    |1 GiB      |
|IOPS de linha de base    |1 IOPS por GiB, até 100.000|
|IOPS de segurança    |3 x IOPS por GiB, até 100.000|
|Taxa de saída         |60 MiB/s + 0.06 * aprovisionado GiB        |
|Taxa de entrada| 40 MiB/s + 0.04 * aprovisionado GiB |

#### <a name="file-level-limits"></a>Limites de nível de ficheiros

|Área  |Ficheiros Premium  |Padrão de ficheiros |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|IOPS máx por arquivo     |5,000         |1,000|
|Identificadores em simultâneo    |2\.000         |2\.000|
|Entrada  |300 MiB/seg|      Os valores de taxa de transferência de ficheiros padrão|
|Saída   |200 Mib/seg| Os valores de taxa de transferência de ficheiros padrão|
|Débito| Os valores de entrada/saída do ficheiro de premium| Até 60 MiB/seg|