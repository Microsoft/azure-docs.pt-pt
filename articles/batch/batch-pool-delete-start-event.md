---
title: Evento do início de eliminação de conjunto do Azure Batch | Documentos da Microsoft
description: Referência para o evento de início de eliminação de conjunto do Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/14/2018
ms.author: v-junlch
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774544"
---
# <a name="pool-delete-start-event"></a>Evento de início de eliminação de conjunto

 Este evento é emitido quando uma operação de eliminação de conjunto foi iniciado. Uma vez que a eliminação de conjunto é um evento assíncrono, pode esperar um evento de conclusão de eliminação de conjunto a ser emitida uma vez concluída a operação de eliminação.

 O exemplo seguinte mostra o corpo de um evento de início de eliminação de conjunto.

```
{
    "id": "myPool1"
}
```

|Elemento|Type|Notas|
|-------------|----------|-----------|
|ID|String|O id do conjunto.|

<!-- Update_Description: update metedata properties -->