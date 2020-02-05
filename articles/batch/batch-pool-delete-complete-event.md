---
title: Piscina de Lote Azure elimina evento completo
description: Referência de exclusão do pool do lote evento concluído. Esse evento é emitido quando uma operação de exclusão de pool é concluída.
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023621"
---
# <a name="pool-delete-complete-event"></a>Evento de conclusão de eliminação de conjunto

 Esse evento é emitido quando uma operação de exclusão de pool é concluída.

 O exemplo a seguir mostra o corpo de um evento de exclusão de pool concluído.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|`id`|Cadeia|A ID do pool.|
|`startTime`|DateTime|A hora em que a exclusão do pool foi iniciada.|
|`endTime`|DateTime|A hora em que a exclusão do pool foi concluída.|

## <a name="remarks"></a>Observações
Para obter mais informações sobre Estados e códigos de erro para a operação de redimensionamento do pool, consulte [excluir um pool de uma conta](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
