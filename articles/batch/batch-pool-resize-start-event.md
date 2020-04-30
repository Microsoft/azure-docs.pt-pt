---
title: Evento de redimensionamento da piscina do Lote Azure
description: Referência para evento de redimensionamento de piscina de lote. O exemplo mostra o corpo de um evento de arranque de redimensionamento de piscina para uma piscina redimensionando de 0 a 2 nódosos com um redimensionamento manual.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: b4aa503c5dfbe00a77216277bdaf7e4c0dc3b1bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115878"
---
# <a name="pool-resize-start-event"></a>Evento de início de redimensionamento de conjunto

 Este evento é emitido quando uma piscina redimensionada. Uma vez que o redimensionado da piscina é um evento assíncrono, você pode esperar que um evento completo de redimensionar a piscina seja emitido assim que a operação de redimensionar estiver concluída.

 O exemplo seguinte mostra o corpo de um evento de arranque de redimensionamento de piscina para uma piscina redimensionando de 0 a 2 nódosos com um redimensionamento manual.

```
{
    "id": "myPool1",
    "nodeDeallocationOption": "Invalid",
    "currentDedicatedNodes": 0,
    "targetDedicatedNodes": 2,
    "currentLowPriorityNodes": 0,
    "targetLowPriorityNodes": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|`id`|String|A identificação da piscina.|
|`nodeDeallocationOption`|String|Especifica quando os nós podem ser removidos da piscina, se o tamanho da piscina estiver a diminuir.<br /><br /> Os valores possíveis são:<br /><br /> **refilar** – Terminar tarefas de execução e refazer filas. As tarefas voltarão a funcionar quando o trabalho estiver ativado. Remova os nódosos assim que as tarefas forem terminadas.<br /><br /> **terminar** – Terminar tarefas de execução. As tarefas não voltarão a funcionar. Remova os nódosos assim que as tarefas forem terminadas.<br /><br /> **conclusão de tarefas** – Permitir que as tarefas em execução atual se concretizem. Não agende novas tarefas enquanto espera. Retire os nós quando todas as tarefas estiverem concluídas.<br /><br /> **Dados retidos** - Permita que as tarefas em execução atualmente completem e, em seguida, aguarde que todos os períodos de retenção de dados de tarefas expirem. Não agende novas tarefas enquanto espera. Remova os nós quando todos os períodos de retenção de tarefas tiverem expirado.<br /><br /> O valor padrão é a refila.<br /><br /> Se o tamanho da piscina está a aumentar, então o valor é definido para **inválido**.|
|`currentDedicatedNodes`|Int32|O número de nós computacionais atualmente atribuídos à piscina.|
|`targetDedicatedNodes`|Int32|O número de nós de computação que são solicitados para a piscina.|
|`currentLowPriorityNodes`|Int32|O número de nós computacionais atualmente atribuídos à piscina.|
|`targetLowPriorityNodes`|Int32|O número de nós de computação que são solicitados para a piscina.|
|`enableAutoScale`|Booleano|Especifica se o tamanho da piscina se ajusta automaticamente ao longo do tempo.|
|`isAutoPool`|Booleano|Especifica se a piscina foi criada através do mecanismo autopool de um trabalho.|
