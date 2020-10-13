---
title: Piscina Azure Batch redimensiona evento completo
description: Referência para lote pool redimensionar evento completo. Veja um exemplo de uma piscina que aumentou de tamanho e completou com sucesso.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: 94301f29fb6e7968dbe0389754fcf2a3b105d7ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83723821"
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
|`id`|Cadeia|A identificação da piscina.|
|`nodeDeallocationOption`|Cadeia|Especifica quando os nós podem ser removidos da piscina, se o tamanho da piscina estiver a diminuir.<br /><br /> Os valores possíveis são:<br /><br /> **requeue** – Termine as tarefas de execução e requei-as. As tarefas voltarão a funcionar quando o trabalho estiver ativado. Retire os nós assim que as tarefas terminarem.<br /><br /> **terminar** – Terminar tarefas de execução. As tarefas não voltarão a funcionar. Retire os nós assim que as tarefas terminarem.<br /><br /> **taskcompletion** – Permitir a execução das tarefas atualmente para completar. Não agende novas tarefas enquanto espera. Retire os nós quando todas as tarefas estiverem concluídas.<br /><br /> **Retenção de dados** - Permita que as tarefas atualmente em execução se concretizem e, em seguida, aguarde que todos os períodos de retenção de dados de tarefas expirem. Não agende novas tarefas enquanto espera. Retire os nós quando todos os períodos de retenção de tarefas tiverem expirado.<br /><br /> O valor predefinido é requeue.<br /><br /> Se o tamanho da piscina estiver a aumentar, o valor está definido para **inválido**.|
|`currentDedicatedNodes`|Int32|O número de nós computacional dedicados atualmente atribuídos à piscina.|
|`targetDedicatedNodes`|Int32|O número de nós computacional dedicados que são solicitados para a piscina.|
|`currentLowPriorityNodes`|Int32|O número de nós de computação de baixa prioridade atualmente atribuídos à piscina.|
|`targetLowPriorityNodes`|Int32|O número de nós de computação de baixa prioridade que são solicitados para a piscina.|
|`enableAutoScale`|Booleano|Especifica se o tamanho da piscina se ajusta automaticamente ao longo do tempo.|
|`isAutoPool`|Booleano|Especifica se a piscina foi criada através do mecanismo autoPool de um trabalho.|
|`startTime`|DateTime|A hora em que a piscina redimensionou.|
|`endTime`|DateTime|O tempo de redimensionar a piscina.|
|`resultCode`|Cadeia|O resultado do redimensionar.|
|`resultMessage`|Cadeia| Uma mensagem detalhada sobre o resultado.<br /><br /> Se o redimensionado concluído com sucesso, afirma que a operação foi bem sucedida.|
