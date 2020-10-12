---
title: Piscina Azure Batch delete evento completo
description: Referência para piscina lote eliminar evento completo. Este evento é emitido quando uma operação de eliminação de piscinas tiver terminado.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: f00d32fd5e4eb49fddf4975a9b64ce792d0226dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85962463"
---
# <a name="pool-delete-complete-event"></a>Evento de conclusão de eliminação de conjunto

 Este evento é emitido quando uma operação de eliminação de piscinas tiver terminado.

 O exemplo a seguir mostra o corpo de uma piscina apagar evento completo.

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
|`startTime`|DateTime|A hora em que a piscina apaga começou.|
|`endTime`|DateTime|O tempo de eliminação da piscina terminou.|

## <a name="remarks"></a>Observações
Para obter mais informações sobre estados e códigos de erro para a operação de redimensionar a piscina, consulte [Eliminar um pool de uma conta](/rest/api/batchservice/delete-a-pool-from-an-account).
