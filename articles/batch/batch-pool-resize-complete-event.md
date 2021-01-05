---
title: Piscina Azure Batch redimensiona evento completo
description: Referência para lote pool redimensionar evento completo. Veja um exemplo de uma piscina que aumentou de tamanho e completou com sucesso.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 9d3342587b5f6e0e134f4295a8c79deeb23df94b
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803651"
---
# <a name="pool-resize-complete-event"></a>Evento de conclusão de redimensionamento de conjunto

 Este evento é emitido quando um redimensionado da piscina tiver concluído ou falhado.

 O exemplo a seguir mostra o corpo de uma piscina redimensionar evento completo para uma piscina que aumentou de tamanho e completou com sucesso.

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
|`id`|String|A identificação da piscina.|
|`nodeDeallocationOption`|String|Especifica quando os nós podem ser removidos da piscina, se o tamanho da piscina estiver a diminuir.<br /><br /> Os valores possíveis são:<br /><br /> **requeue** – Termine as tarefas de execução e requei-as. As tarefas voltarão a funcionar quando o trabalho estiver ativado. Retire os nós assim que as tarefas terminarem.<br /><br /> **terminar** – Terminar tarefas de execução. As tarefas não voltarão a funcionar. Retire os nós assim que as tarefas terminarem.<br /><br /> **taskcompletion** – Permitir a execução das tarefas atualmente para completar. Não agende novas tarefas enquanto espera. Retire os nós quando todas as tarefas estiverem concluídas.<br /><br /> **Retenção de dados** - Permita que as tarefas atualmente em execução se concretizem e, em seguida, aguarde que todos os períodos de retenção de dados de tarefas expirem. Não agende novas tarefas enquanto espera. Retire os nós quando todos os períodos de retenção de tarefas tiverem expirado.<br /><br /> O valor predefinido é requeue.<br /><br /> Se o tamanho da piscina estiver a aumentar, o valor está definido para **inválido**.|
|`currentDedicatedNodes`|Int32|O número de nós computacional dedicados atualmente atribuídos à piscina.|
|`targetDedicatedNodes`|Int32|O número de nós computacional dedicados que são solicitados para a piscina.|
|`currentLowPriorityNodes`|Int32|O número de nós de computação de baixa prioridade atualmente atribuídos à piscina.|
|`targetLowPriorityNodes`|Int32|O número de nós de computação de baixa prioridade que são solicitados para a piscina.|
|`enableAutoScale`|Booleano|Especifica se o tamanho da piscina se ajusta automaticamente ao longo do tempo.|
|`isAutoPool`|Booleano|Especifica se a piscina foi criada através do mecanismo autoPool de um trabalho.|
|`startTime`|DateTime|A hora em que a piscina redimensionou.|
|`endTime`|DateTime|O tempo de redimensionar a piscina.|
|`resultCode`|String|O resultado do redimensionar.|
|`resultMessage`|String| Uma mensagem detalhada sobre o resultado.<br /><br /> Se o redimensionado concluído com sucesso, afirma que a operação foi bem sucedida.|
