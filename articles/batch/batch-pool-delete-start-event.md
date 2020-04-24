---
title: Piscina de Lote Azure elimina evento de início
description: Referência para lote piscina excluir evento de início. Este evento é emitido quando uma operação de eliminação de piscinas tiver começado.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 38e419e549006d3fde2f1694e0d40e620cd438e5
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115929"
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
