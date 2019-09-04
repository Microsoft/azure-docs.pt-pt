---
title: Evento de início de exclusão do pool do lote do Azure | Microsoft Docs
description: Referência do evento de início de exclusão do pool do lote.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 76f3a15d88166160f28461c34ee03ff7c8880a59
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258422"
---
# <a name="pool-delete-start-event"></a>Evento de início de eliminação de conjunto

 Esse evento é emitido quando uma operação de exclusão de pool é iniciada. Como a exclusão do pool é um evento assíncrono, você pode esperar que um evento de exclusão de pool completo seja emitido quando a operação de exclusão for concluída.

 O exemplo a seguir mostra o corpo de um evento de início de exclusão de pool.

```
{
    "id": "myPool1"
}
```

|Elemento|Type|Notas|
|-------------|----------|-----------|
|`id`|Cadeia|A ID do pool.|
