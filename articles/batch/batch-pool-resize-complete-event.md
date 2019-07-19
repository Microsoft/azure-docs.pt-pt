---
title: Evento de conclusão de redimensionamento do pool do lote do Azure | Microsoft Docs
description: Referência para o evento de redimensionamento de pool do lote concluído.
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
ms.openlocfilehash: c2544bd2be683b731c3dac0bea651d4b64dff75e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323193"
---
# <a name="pool-resize-complete-event"></a>Evento de conclusão de redimensionamento de conjunto

 Esse evento é emitido quando um redimensionamento de pool é concluído ou falhou.

 O exemplo a seguir mostra o corpo de um evento completo de redimensionamento de pool para um pool que aumentou de tamanho e foi concluído com êxito.

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|Elemento|Type|Notas|
|-------------|----------|-----------|
|id|Cadeia|A ID do pool.|
|nodeDeallocationOption|Cadeia|Especifica quando os nós podem ser removidos do pool, se o tamanho do pool estiver diminuindo.<br /><br /> Os valores possíveis são:<br /><br /> recolocar em **fila** – termine as tarefas em execução e recoloca-as na fila. As tarefas serão executadas novamente quando o trabalho for habilitado. Remova os nós assim que as tarefas forem encerradas.<br /><br /> **terminar** – encerrar tarefas em execução. As tarefas não serão executadas novamente. Remova os nós assim que as tarefas forem encerradas.<br /><br /> **taskcompletion** – permitir que as tarefas atualmente em execução sejam concluídas. Não agendar nenhuma nova tarefa enquanto aguarda. Remova os nós quando todas as tarefas tiverem sido concluídas.<br /><br /> **Retaineddata** – permitir que as tarefas atualmente em execução sejam concluídas e aguarde até que todos os períodos de retenção de dados da tarefa expirem. Não agendar nenhuma nova tarefa enquanto aguarda. Remova os nós quando todos os períodos de retenção de tarefa tiverem expirado.<br /><br /> O valor padrão é recolocar na fila.<br /><br /> Se o tamanho do pool estiver aumentando, o valor será definido como **inválido**.|
|currentDedicated|Int32|O número de nós de computação atribuídos ao pool no momento.|
|targetDedicated|Int32|O número de nós de computação que são solicitados para o pool.|
|enableAutoScale|Bool|Especifica se o tamanho do pool se ajusta automaticamente ao longo do tempo.|
|isAutoPool|Bool|Especifica se o pool foi criado por meio do mecanismo de autopool de um trabalho.|
|startTime|DateTime|A hora em que o redimensionamento do pool foi iniciado.|
|endTime|DateTime|A hora em que o redimensionamento do pool foi concluído.|
|resultCode|Cadeia|O resultado do redimensionamento.|
|resultMessage|Cadeia|O erro de redimensionamento inclui os detalhes do resultado.<br /><br /> Se o redimensionamento for concluído com êxito, ele indicará que a operação foi bem-sucedida.|