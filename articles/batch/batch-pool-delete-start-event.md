---
title: Piscina de Lote Azure elimina evento de início
description: Referência para lote piscina excluir evento de início. Este evento é emitido quando uma operação de eliminação de piscinas tiver começado.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022210"
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
|`id`|Cadeia|A identificação da piscina.|
