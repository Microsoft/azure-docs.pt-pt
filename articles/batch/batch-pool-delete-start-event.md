---
title: Piscina de Lote Azure elimina evento de início
description: Referência do evento de início de exclusão do pool do lote. Esse evento é emitido quando uma operação de exclusão de pool é iniciada.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 24a68c6656bd13f0c353d53870a51cdc940fd141
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022210"
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
|`id`|Cadeia|A ID do pool.|
