---
title: Evento de redimensionamento completo do pool do lote do Azure
description: Referência para o evento de redimensionamento de pool do lote concluído. Veja um exemplo de um pool que aumentou de tamanho e foi concluído com êxito.
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022227"
---
# <a name="pool-resize-complete-event"></a>Evento de conclusão de redimensionamento de conjunto

 Esse evento é emitido quando um redimensionamento de pool é concluído ou falhou.

 O exemplo a seguir mostra o corpo de um evento completo de redimensionamento de pool para um pool que aumentou de tamanho e foi concluído com êxito.

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
|`id`|Cadeia|A ID do pool.|
|`nodeDeallocationOption`|Cadeia|Especifica quando os nós podem ser removidos do pool, se o tamanho do pool estiver diminuindo.<br /><br /> Os valores possíveis são:<br /><br /> recolocar em **fila** – termine as tarefas em execução e recoloca-as na fila. As tarefas serão executadas novamente quando o trabalho for habilitado. Remova os nós assim que as tarefas forem encerradas.<br /><br /> **terminar** – encerrar tarefas em execução. As tarefas não serão executadas novamente. Remova os nós assim que as tarefas forem encerradas.<br /><br /> **taskcompletion** – permitir que as tarefas atualmente em execução sejam concluídas. Não agendar nenhuma nova tarefa enquanto aguarda. Remova os nós quando todas as tarefas tiverem sido concluídas.<br /><br /> **Retaineddata** – permitir que as tarefas atualmente em execução sejam concluídas e aguarde até que todos os períodos de retenção de dados da tarefa expirem. Não agendar nenhuma nova tarefa enquanto aguarda. Remova os nós quando todos os períodos de retenção de tarefa tiverem expirado.<br /><br /> O valor padrão é recolocar na fila.<br /><br /> Se o tamanho do pool estiver aumentando, o valor será definido como **inválido**.|
|`currentDedicatedNodes`|Int32|O número de nós de computação dedicados atribuídos ao pool no momento.|
|`targetDedicatedNodes`|Int32|O número de nós de computação dedicados que são solicitados para o pool.|
|`currentLowPriorityNodes`|Int32|O número de nós de computação de baixa prioridade atualmente atribuídos ao pool.|
|`targetLowPriorityNodes`|Int32|O número de nós de computação de baixa prioridade que são solicitados para o pool.|
|`enableAutoScale`|Bool|Especifica se o tamanho do pool se ajusta automaticamente ao longo do tempo.|
|`isAutoPool`|Bool|Especifica se o pool foi criado por meio do mecanismo de autopool de um trabalho.|
|`startTime`|DateTime|A hora em que o redimensionamento do pool foi iniciado.|
|`endTime`|DateTime|A hora em que o redimensionamento do pool foi concluído.|
|`resultCode`|Cadeia|O resultado do redimensionamento.|
|`resultMessage`|Cadeia| Uma mensagem detalhada sobre o resultado.<br /><br /> Se o redimensionamento for concluído com êxito, ele indicará que a operação foi bem-sucedida.|
