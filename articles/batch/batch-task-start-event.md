---
title: Evento de início de tarefa de Azure Batch
description: Informações de referência para o evento de início de tarefa do Lote. Este evento é emitido uma vez que uma tarefa foi agendada para começar em um nó de computação pelo agendador.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: ed7b3014279fc87fbaae6f1c270f056e95073aaf
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965098"
---
# <a name="task-start-event"></a>Evento de início de tarefa

 Este evento é emitido uma vez que uma tarefa foi agendada para começar em um nó de computação pelo agendador. Note que se a tarefa for novamente julgada ou requeada este evento será novamente emitido para a mesma tarefa, mas a contagem de repetição e a versão de tarefa do sistema serão atualizadas em conformidade.


 O exemplo a seguir mostra o corpo de um evento de arranque de tarefa.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 220192842,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "retryCount": 0
    }
}
```

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`jobId`|String|A identificação do trabalho que contém a tarefa.|
|`id`|String|A identificação da tarefa.|
|`taskType`|String|O tipo de tarefa. Isto pode ser 'JobManager' indicando que é uma tarefa de gestor de emprego ou 'Utilizador' indicando que não é uma tarefa de gerente de emprego.|
|`systemTaskVersion`|Int32|Este é o contra-ataque interno de uma tarefa. Internamente, o serviço Batch pode voltar a tentar uma tarefa para responder a problemas transitórios. Estas questões podem incluir erros de agendamento internos ou tentativas de recuperação de nós de computação em mau estado.|
|[`nodeInfo`](#nodeInfo)|Tipo Complexo|Contém informações sobre o nó de computação em que a tarefa foi executada.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Tipo Complexo|Especifica que a tarefa é tarefa multi-instância que requer múltiplos nós de computação.  Consulte [as multiInstanceSettings](/rest/api/batchservice/get-information-about-a-task) para obter mais detalhes.|
|[`constraints`](#constraints)|Tipo Complexo|Os constrangimentos de execução que se aplicam a esta tarefa.|
|[`executionInfo`](#executionInfo)|Tipo Complexo|Contém informações sobre a execução da tarefa.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a>nodeInfo

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`poolId`|String|A identificação da piscina em que a tarefa funcionou.|
|`nodeId`|String|A identificação do nó em que a tarefa funcionou.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a>multiInstanceSettings

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`numberOfInstances`|int|O número de nós de computação exigidos pela tarefa.|

###  <a name="constraints"></a><a name="constraints"></a>constrangimentos

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|O número máximo de vezes que a tarefa pode ser novamente julgado. O serviço Batch retrição uma tarefa se o seu código de saída não for zero.<br /><br /> Note que este valor controla especificamente o número de retrações. O serviço Batch tentará a tarefa uma vez e poderá voltar a tentar até este limite. Por exemplo, se a contagem máxima de repetição for 3, Batch tenta uma tarefa até 4 vezes (uma tentativa inicial e 3 retrórias).<br /><br /> Se a contagem máxima de repetição for 0, o serviço Batch não relemca as tarefas.<br /><br /> Se a contagem máxima de repetição for de -1, o serviço de recauchutagem do Lote tarefas sem limite.<br /><br /> O valor predefinido é 0 (sem retrações).|

###  <a name="executioninfo"></a><a name="executionInfo"></a>execuçãoInfo

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`retryCount`|Int32|O número de vezes que a tarefa foi novamente experimentada pelo serviço Batch. A tarefa é novamente julgada se sair com um código de saída não zero, até ao MaxTaskRetryCount especificado|
