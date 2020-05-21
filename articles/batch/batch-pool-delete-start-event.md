---
title: Piscina de Lote Azure elimina evento de início
description: Referência para lote piscina excluir evento de início. Este evento é emitido quando uma operação de eliminação de piscinas tiver começado.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: d4fac833b6c77258e48b38838f6b7b133738a6e8
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723940"
---
# <a name="pool-delete-start-event"></a>Evento de início de eliminação de conjunto

 Este evento é emitido quando uma operação de eliminação de piscinas tiver começado. Uma vez que a eliminação da piscina é um evento assíncrono, pode esperar que um evento completo de piscina seja emitido um evento completo assim que a operação de exclusão esteja concluída.

 O exemplo seguinte mostra o corpo de uma piscina apagar evento de início.

```
{
    "id": "myPool1"
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|`id`|String|A identificação da piscina.|
