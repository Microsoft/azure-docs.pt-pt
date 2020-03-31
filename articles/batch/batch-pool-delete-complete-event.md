---
title: Piscina de Lote Azure elimina evento completo
description: Referência para piscina de lote excluir evento completo. Este evento é emitido quando uma operação de eliminação de piscina sabotada.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d317d7395a8246c109073a72338b55960cb50954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023621"
---
# <a name="pool-delete-complete-event"></a>Evento de conclusão de eliminação de conjunto

 Este evento é emitido quando uma operação de eliminação de piscina sabotada.

 O exemplo seguinte mostra o corpo de uma piscina apagar evento completo.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|`id`|Cadeia|A identificação da piscina.|
|`startTime`|DateTime|O tempo que a piscina apagou começou.|
|`endTime`|DateTime|O tempo que a piscina apagar está concluído.|

## <a name="remarks"></a>Observações
Para obter mais informações sobre estados e códigos de erro para a operação de redimensionação do pool, consulte [Eliminar uma piscina de uma conta](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
