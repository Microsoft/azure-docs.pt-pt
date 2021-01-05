---
title: Piscina Azure Batch delete evento completo
description: Referência para piscina lote eliminar evento completo. Este evento é emitido quando uma operação de eliminação de piscinas tiver terminado.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: be6411a150ae6be424c0621eed768157154c7408
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803736"
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
|`id`|String|A identificação da piscina.|
|`startTime`|DateTime|A hora em que a piscina apaga começou.|
|`endTime`|DateTime|O tempo de eliminação da piscina terminou.|

## <a name="remarks"></a>Observações

Para obter mais informações sobre estados e códigos de erro para a operação de redimensionar a piscina, consulte [Eliminar um pool de uma conta](/rest/api/batchservice/delete-a-pool-from-an-account).
