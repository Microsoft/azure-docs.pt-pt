---
title: Evento de início de exclusão do pool do lote do Azure | Microsoft Docs
description: Referência do evento de início de exclusão do pool do lote.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 65d20f2194b2bf83ecf32e19c1ab5b0f7bc7a004
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323221"
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
|id|Cadeia|A ID do pool.|