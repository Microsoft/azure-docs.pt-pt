---
title: Piscina Azure Batch redimensiona evento de início
description: Referência para lote pool redimensionar evento de início. O exemplo mostra o corpo de um evento de arranque de redimensionamento da piscina para uma piscina redimensionando de 0 a 2 nós com um redimensionamento manual.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: be64a2ef30cbe3c404633b29202a4adf1e49ea9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803617"
---
# <a name="pool-resize-start-event"></a>Evento de início de redimensionamento de conjunto

 Este evento é emitido quando uma piscina redimensionar começou. Uma vez que o redimensionar a piscina é um evento assíncronos, você pode esperar que um evento completo de redimensione piscina seja emitido uma vez que a operação de redimensionar completa.

 O exemplo a seguir mostra o corpo de um evento de arranque de piscina para uma piscina redimensionada de 0 a 2 nós com um redimensionamento manual.

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
|`nodeDeallocationOption`|String|Especifica quando os nós podem ser removidos da piscina, se o tamanho da piscina estiver a diminuir.<br /><br /> Os valores possíveis são:<br /><br /> **requeue** – Termine as tarefas de execução e requei-as. As tarefas voltarão a funcionar quando o trabalho estiver ativado. Retire os nós assim que as tarefas terminarem.<br /><br /> **terminar** – Terminar tarefas de execução. As tarefas não voltarão a funcionar. Retire os nós assim que as tarefas terminarem.<br /><br /> **taskcompletion** – Permitir a execução das tarefas atualmente para completar. Não agende novas tarefas enquanto espera. Retire os nós quando todas as tarefas estiverem concluídas.<br /><br /> **Retenção de dados** - Permita que as tarefas atualmente em execução se concretizem e, em seguida, aguarde que todos os períodos de retenção de dados de tarefas expirem. Não agende novas tarefas enquanto espera. Retire os nós quando todos os períodos de retenção de tarefas tiverem expirado.<br /><br /> O valor predefinido é requeue.<br /><br /> Se o tamanho da piscina estiver a aumentar, o valor está definido para **inválido**.|
|`currentDedicatedNodes`|Int32|O número de nós computacional atualmente atribuídos à piscina.|
|`targetDedicatedNodes`|Int32|O número de nós computacional que são solicitados para a piscina.|
|`currentLowPriorityNodes`|Int32|O número de nós computacional atualmente atribuídos à piscina.|
|`targetLowPriorityNodes`|Int32|O número de nós computacional que são solicitados para a piscina.|
|`enableAutoScale`|Booleano|Especifica se o tamanho da piscina se ajusta automaticamente ao longo do tempo.|
|`isAutoPool`|Booleano|Especifica se a piscina foi criada através do mecanismo autoPool de um trabalho.|
