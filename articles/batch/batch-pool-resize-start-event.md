---
title: Eventos de início de redimensionamento de conjunto de Batch do Azure | Documentos da Microsoft
description: Referência para o evento de início de redimensionamento de conjunto do Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 63abeaca5a9c87945671e79a9c8d7a2512d0d777
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774238"
---
# <a name="pool-resize-start-event"></a>Evento de início de redimensionamento de conjunto

 Este evento é emitido quando um redimensionamento de conjunto foi iniciado. Uma vez que o redimensionamento de conjunto é um evento assíncrono, pode esperar um evento de conclusão de redimensionamento de conjunto a ser emitida uma vez concluída a operação de redimensionamento.

 O exemplo seguinte mostra o corpo de um evento de início de redimensionamento de conjunto para um conjunto de redimensionamento de 0 a 2 nós com um redimensionamento manual.

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Elemento|Type|Notas|
|-------------|----------|-----------|
|poolId|String|O id do conjunto.|
|nodeDeallocationOption|String|Especifica quando nós podem ser removidos do conjunto, se estiver a diminuir o tamanho do conjunto.<br /><br /> Os valores possíveis são:<br /><br /> **recolocação na fila** – terminar a execução de tarefas e de recolocações na fila-los. As tarefas serão executadas novamente quando a tarefa está ativada. Remova nós assim que as tarefas forem terminadas.<br /><br /> **terminar** – terminar tarefas em execução. As tarefas não serão executadas novamente. Remova nós assim que as tarefas forem terminadas.<br /><br /> **taskcompletion** – permitir tarefas atualmente em execução para concluir. Não agende novas tarefas enquanto espera. Remova nós quando todas as tarefas estiverem concluídas.<br /><br /> **Retaineddata** -permitir tarefas sejam concluídas e aguardar que todas as tarefas de períodos de retenção de dados para expirar atualmente em execução. Não agende novas tarefas enquanto espera. Remova nós quando todos os períodos de retenção de tarefas tiverem expirado.<br /><br /> O valor predefinido é recolocação na fila.<br /><br /> Se está a aumentar o tamanho do conjunto, em seguida, o valor é definido como **inválido**.|
|currentDedicated|Int32|O número de nós de computação atualmente atribuídos ao agrupamento.|
|targetDedicated|Int32|O número de nós de computação que são pedidos para o conjunto.|
|enableAutoScale|Booleano|Especifica se o tamanho do conjunto se ajusta automaticamente ao longo do tempo.|
|isAutoPool|Booleano|Especifica se o conjunto foi criado por meio de mecanismo de AutoPool de uma tarefa.|
