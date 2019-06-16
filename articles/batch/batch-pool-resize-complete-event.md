---
title: Evento de conclusão de redimensionamento de conjunto do Batch do Azure | Documentos da Microsoft
description: Referência para o conjunto do Batch redimensionar o evento de conclusão.
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
ms.openlocfilehash: 87c98b89a49adbad88841dccbd4ba47d370b2be7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60776427"
---
# <a name="pool-resize-complete-event"></a>Evento de conclusão de redimensionamento de conjunto

 Este evento é emitido quando um redimensionamento de conjunto concluiu ou não.

 O exemplo seguinte mostra o corpo de um evento de conclusão de redimensionamento de conjunto para um conjunto que aumenta de tamanho e foi concluída com êxito.

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

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|id|String|O id do conjunto.|
|nodeDeallocationOption|String|Especifica quando nós podem ser removidos do conjunto, se estiver a diminuir o tamanho do conjunto.<br /><br /> Os valores possíveis são:<br /><br /> **recolocação na fila** – terminar a execução de tarefas e de recolocações na fila-los. As tarefas serão executadas novamente quando a tarefa está ativada. Remova nós assim que as tarefas forem terminadas.<br /><br /> **terminar** – terminar tarefas em execução. As tarefas não serão executadas novamente. Remova nós assim que as tarefas forem terminadas.<br /><br /> **taskcompletion** – permitir tarefas atualmente em execução para concluir. Não agende novas tarefas enquanto espera. Remova nós quando todas as tarefas estiverem concluídas.<br /><br /> **Retaineddata** -permitir tarefas sejam concluídas e aguardar que todas as tarefas de períodos de retenção de dados para expirar atualmente em execução. Não agende novas tarefas enquanto espera. Remova nós quando todos os períodos de retenção de tarefas tiverem expirado.<br /><br /> O valor predefinido é recolocação na fila.<br /><br /> Se está a aumentar o tamanho do conjunto, em seguida, o valor é definido como **inválido**.|
|currentDedicated|Int32|O número de nós de computação atualmente atribuídos ao agrupamento.|
|targetDedicated|Int32|O número de nós de computação que são pedidos para o conjunto.|
|enableAutoScale|Booleano|Especifica se o tamanho do conjunto se ajusta automaticamente ao longo do tempo.|
|isAutoPool|Booleano|Especifica se o conjunto foi criado por meio de mecanismo de AutoPool de uma tarefa.|
|startTime|DateTime|O tempo de redimensionar o conjunto foi iniciada.|
|endTime|DateTime|O tempo de redimensionar o conjunto foi concluído.|
|resultCode|String|O resultado do redimensionamento.|
|resultMessage|String|O erro de redimensionamento inclui os detalhes do resultado.<br /><br /> Se o redimensionamento foi concluída com êxito, Estados de que a operação foi concluída com êxito.|