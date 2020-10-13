---
title: Evento completo de tarefas Azure Batch
description: Referência para evento completo de tarefas batch. Este evento é emitido uma vez que uma tarefa esteja concluída, independentemente do código de saída.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 11f727b07723f32cd08130b4af17e57ede3d264f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850896"
---
# <a name="task-complete-event"></a>Evento de conclusão de tarefa

 Este evento é emitido uma vez que uma tarefa esteja concluída, independentemente do código de saída. Este evento pode ser usado para determinar a duração de uma tarefa, onde a tarefa foi e se foi novamente julgada.


 O exemplo a seguir mostra o corpo de um evento completo de tarefas.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
    "requiredSlots": 1,
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
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`jobId`|Cadeia|A identificação do trabalho que contém a tarefa.|
|`id`|Cadeia|A identificação da tarefa.|
|`taskType`|Cadeia|O tipo de tarefa. Isto pode ser 'JobManager' indicando que é uma tarefa de gestor de emprego ou 'Utilizador' indicando que não é uma tarefa de gerente de emprego. Este evento não é emitido para tarefas de preparação de emprego, tarefas de libertação de emprego ou tarefas de início.|
|`systemTaskVersion`|Int32|Este é o contra-ataque interno de uma tarefa. Internamente, o serviço Batch pode voltar a tentar uma tarefa para responder a problemas transitórios. Estas questões podem incluir erros de agendamento internos ou tentativas de recuperação de nós de computação em mau estado.|
|`requiredSlots`|Int32|As ranhuras necessárias para executar a tarefa.|
|[`nodeInfo`](#nodeInfo)|Tipo Complexo|Contém informações sobre o nó de computação em que a tarefa foi executada.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Tipo Complexo|Especifica que a tarefa é uma Tarefa multi-instância que requer múltiplos nós de computação.  Veja [`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task) mais detalhes.|
|[`constraints`](#constraints)|Tipo Complexo|Os constrangimentos de execução que se aplicam a esta tarefa.|
|[`executionInfo`](#executionInfo)|Tipo Complexo|Contém informações sobre a execução da tarefa.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`poolId`|Cadeia|A identificação da piscina em que a tarefa funcionou.|
|`nodeId`|Cadeia|A identificação do nó em que a tarefa funcionou.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|O número de nós de computação exigidos pela tarefa.|

###  <a name="constraints"></a><a name="constraints"></a> constrangimentos

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|O número máximo de vezes que a tarefa pode ser novamente julgado. O serviço Batch retrição uma tarefa se o seu código de saída não for zero.<br /><br /> Note que este valor controla especificamente o número de retrações. O serviço Batch tentará a tarefa uma vez e poderá voltar a tentar até este limite. Por exemplo, se a contagem máxima de repetição for 3, Batch tenta uma tarefa até 4 vezes (uma tentativa inicial e 3 retrórias).<br /><br /> Se a contagem máxima de repetição for 0, o serviço Batch não relemca as tarefas.<br /><br /> Se a contagem máxima de repetição for de -1, o serviço de recauchutagem do Lote tarefas sem limite.<br /><br /> O valor predefinido é 0 (sem retrações).|

###  <a name="executioninfo"></a><a name="executionInfo"></a> execuçãoInfo

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`startTime`|DateTime|O momento em que a tarefa começou a decorrer. 'Running' corresponde ao estado **de funcionamento,** pelo que se a tarefa especificar ficheiros de recursos ou pacotes de aplicações, então a hora de início reflete o momento em que a tarefa começou a descarregar ou a implementar.  Se a tarefa tiver sido reiniciada ou novamente julgada, esta é a hora mais recente em que a tarefa começou a ser iniciada.|
|`endTime`|DateTime|O momento em que a tarefa terminou.|
|`exitCode`|Int32|O código de saída da tarefa.|
|`retryCount`|Int32|O número de vezes que a tarefa foi novamente experimentada pelo serviço Batch. A tarefa é novamente julgada se sair com um código de saída não zero, até ao MaxTaskRetryCount especificado.|
|`requeueCount`|Int32|O número de vezes que a tarefa foi requeada pelo serviço Batch como resultado de um pedido do utilizador.<br /><br /> Quando o utilizador remove os nós de uma piscina (redimensionando ou diminuindo a piscina) ou quando o trabalho está a ser desativado, o utilizador pode especificar que as tarefas de execução dos nós são requeadas para execução. Esta contagem acompanha quantas vezes a tarefa foi requeada por estas razões.|
