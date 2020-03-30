---
title: Piscina de Lote Azure redimensiona evento completo
description: Referência para lote de piscina redimensionar evento completo. Veja um exemplo de uma piscina que aumentou de tamanho e completou com sucesso.
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
ms.openlocfilehash: e2c66471ad9fe8d917d1ffddceb6e01c339d62dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022227"
---
# <a name="pool-resize-complete-event"></a>Evento de conclusão de redimensionamento de conjunto

 Este evento é emitido quando um redimensionado de piscina tiver concluído ou falhado.

 O exemplo seguinte mostra o corpo de uma piscina redimensionando evento completo para uma piscina que aumentou de tamanho e completou com sucesso.

```
{
    "id": "myPool",
    "nodeDeallocationOption": "invalid",
        "currentDedicatedNodes": 10,
        "targetDedicatedNodes": 10,
    "currentLowPriorityNodes": 5,
        "targetLowPriorityNodes": 5,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "resultCode": "Success",
    "resultMessage": "The operation succeeded"
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|`id`|Cadeia|A identificação da piscina.|
|`nodeDeallocationOption`|Cadeia|Especifica quando os nós podem ser removidos da piscina, se o tamanho da piscina estiver a diminuir.<br /><br /> Os valores possíveis são:<br /><br /> **refilar** – Terminar tarefas de execução e refazer filas. As tarefas voltarão a funcionar quando o trabalho estiver ativado. Remova os nódosos assim que as tarefas forem terminadas.<br /><br /> **terminar** – Terminar tarefas de execução. As tarefas não voltarão a funcionar. Remova os nódosos assim que as tarefas forem terminadas.<br /><br /> **conclusão de tarefas** – Permitir que as tarefas em execução atual se concretizem. Não agende novas tarefas enquanto espera. Retire os nós quando todas as tarefas estiverem concluídas.<br /><br /> **Dados retidos** - Permita que as tarefas em execução atualmente completem e, em seguida, aguarde que todos os períodos de retenção de dados de tarefas expirem. Não agende novas tarefas enquanto espera. Remova os nós quando todos os períodos de retenção de tarefas tiverem expirado.<br /><br /> O valor padrão é a refila.<br /><br /> Se o tamanho da piscina está a aumentar, então o valor é definido para **inválido**.|
|`currentDedicatedNodes`|Int32|O número de nós de computação dedicados atualmente atribuídos à piscina.|
|`targetDedicatedNodes`|Int32|O número de nós de computação dedicados que são solicitados para a piscina.|
|`currentLowPriorityNodes`|Int32|O número de nós de computação de baixa prioridade atualmente atribuídos à piscina.|
|`targetLowPriorityNodes`|Int32|O número de nós de computação de baixa prioridade que são solicitados para a piscina.|
|`enableAutoScale`|Booleano|Especifica se o tamanho da piscina se ajusta automaticamente ao longo do tempo.|
|`isAutoPool`|Booleano|Especifica se a piscina foi criada através do mecanismo autopool de um trabalho.|
|`startTime`|DateTime|A hora em que a piscina redimensionou.|
|`endTime`|DateTime|O tempo que a piscina redimensionou.|
|`resultCode`|Cadeia|O resultado do redimensionado.|
|`resultMessage`|Cadeia| Uma mensagem detalhada sobre o resultado.<br /><br /> Se o redimensionado concluído com sucesso, afirma que a operação foi bem sucedida.|
