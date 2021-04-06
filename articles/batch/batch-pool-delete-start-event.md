---
title: Piscina Azure Batch apagar evento de início
description: Referência para lote pool eliminar evento de início. Este evento é emitido quando uma operação de eliminação de piscinas foi iniciada.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 86f6eb8e7b269cb45f692398e9e60390375ca073
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803753"
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
