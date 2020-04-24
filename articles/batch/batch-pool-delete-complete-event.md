---
title: Piscina de Lote Azure elimina evento completo
description: Referência para piscina de lote excluir evento completo. Este evento é emitido quando uma operação de eliminação de piscina sabotada.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d28223c79f96f35a6ee11f98e0f09f21d7db4451
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115946"
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
|`id`|String|A identificação da piscina.|
|`startTime`|DateTime|O tempo que a piscina apagou começou.|
|`endTime`|DateTime|O tempo que a piscina apagar está concluído.|

## <a name="remarks"></a>Observações
Para obter mais informações sobre estados e códigos de erro para a operação de redimensionação do pool, consulte [Eliminar uma piscina de uma conta](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
