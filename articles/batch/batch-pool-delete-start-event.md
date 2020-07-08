---
title: Piscina Azure Batch apagar evento de início
description: Referência para lote pool eliminar evento de início. Este evento é emitido quando uma operação de eliminação de piscinas foi iniciada.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: d4fac833b6c77258e48b38838f6b7b133738a6e8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83723940"
---
# <a name="pool-delete-start-event"></a>Evento de início de eliminação de conjunto

 Este evento é emitido quando uma operação de eliminação de piscinas foi iniciada. Uma vez que a eliminação da piscina é um evento assíncronos, pode esperar que uma piscina elimine o evento completo a ser emitido assim que a operação de eliminação estiver concluída.

 O exemplo a seguir mostra o corpo de uma piscina apagar evento de início.

```
{
    "id": "myPool1"
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|`id`|String|A identificação da piscina.|
