---
title: Piscina Azure Batch apagar evento de início
description: Referência para lote pool eliminar evento de início. Este evento é emitido quando uma operação de eliminação de piscinas foi iniciada.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: d4fac833b6c77258e48b38838f6b7b133738a6e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
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
|`id`|Cadeia|A identificação da piscina.|
