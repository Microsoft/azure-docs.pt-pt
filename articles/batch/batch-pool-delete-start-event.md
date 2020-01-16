---
title: Evento de início de exclusão do pool do lote do Azure | Microsoft Docs
description: Referência do evento de início de exclusão do pool do lote.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: d582e2b04d203484632a1781d1819f612de41fe7
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026725"
---
# <a name="pool-delete-start-event"></a>Evento de início de eliminação de conjunto

 Esse evento é emitido quando uma operação de exclusão de pool é iniciada. Como a exclusão do pool é um evento assíncrono, você pode esperar que um evento de exclusão de pool completo seja emitido quando a operação de exclusão for concluída.

 O exemplo a seguir mostra o corpo de um evento de início de exclusão de pool.

```
{
    "id": "myPool1"
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|`id`|String|A ID do pool.|
