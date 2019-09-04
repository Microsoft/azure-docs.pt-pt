---
title: Evento de conclusão de exclusão do pool do lote do Azure | Microsoft Docs
description: Referência de exclusão do pool do lote evento concluído.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 9bf50bd19ca3f4316c4c2ddbcdd3333745ebefd7
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258550"
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

|Elemento|Type|Notas|
|-------------|----------|-----------|
|`id`|Cadeia|A ID do pool.|
|`startTime`|DateTime|A hora em que a exclusão do pool foi iniciada.|
|`endTime`|DateTime|A hora em que a exclusão do pool foi concluída.|

## <a name="remarks"></a>Observações
Para obter mais informações sobre Estados e códigos de erro para a operação de redimensionamento do pool, consulte [excluir um pool de uma conta](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
