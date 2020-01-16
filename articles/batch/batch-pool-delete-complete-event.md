---
title: Evento de conclusão de exclusão do pool do lote do Azure | Microsoft Docs
description: Referência de exclusão do pool do lote evento concluído.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: bcbfe009e2880c0155a067305cc28317678cbfa6
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026688"
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
|`id`|String|A ID do pool.|
|`startTime`|DateTime|A hora em que a exclusão do pool foi iniciada.|
|`endTime`|DateTime|A hora em que a exclusão do pool foi concluída.|

## <a name="remarks"></a>Observações
Para obter mais informações sobre Estados e códigos de erro para a operação de redimensionamento do pool, consulte [excluir um pool de uma conta](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
