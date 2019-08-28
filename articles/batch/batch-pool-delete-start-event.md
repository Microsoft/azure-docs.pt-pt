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
ms.openlocfilehash: 6f8b1bdd680d43cb14707338d2e37e41114126b0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094752"
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