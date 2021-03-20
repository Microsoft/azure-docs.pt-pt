---
title: Azure Batch agenda de tarefas falha evento
description: Referência para o evento de agenda de tarefas do Lote. Este evento é emitido quando uma tarefa não foi agendada e tentará novamente mais tarde.
ms.topic: reference
ms.date: 09/20/2020
ms.openlocfilehash: 549281d2b2c371e8f09c584e771cf44f7abc8a00
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91852191"
---
# <a name="task-schedule-fail-event"></a>Evento de falha de agendamento de tarefa

 Este evento é emitido quando uma tarefa não foi agendada e será novamente julgada mais tarde. Trata-se de uma falha temporária no tempo de agendamento de tarefas devido à limitação de recursos, por exemplo, não há faixas horárias suficientes disponíveis nos nós para executar uma tarefa com `requiredSlots` especificada.

 O exemplo a seguir mostra o corpo de um evento de falha de agenda de tarefas.

```
{
    "jobId": "job-01",
    "id": "task-01",
    "taskType": "User",
    "systemTaskVersion": 665378862,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-01",
        "nodeId": " "
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 0
    },
    "schedulingError": {
        "category": "UserError",
        "code": "JobPreparationTaskFailed",
        "message": "Task cannot run because the job preparation task failed on node"
    }
}
```

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`jobId`|String|A identificação do trabalho que contém a tarefa.|
|`id`|String|A identificação da tarefa.|
|`taskType`|String|O tipo de tarefa. Isto pode ser 'JobManager' indicando que é uma tarefa de gestor de emprego ou 'Utilizador' indicando que não é uma tarefa de gerente de emprego. Este evento não é emitido para tarefas de preparação de emprego, tarefas de libertação de emprego ou tarefas de início.|
|`systemTaskVersion`|Int32|Este é o contra-ataque interno de uma tarefa. Internamente, o serviço Batch pode voltar a tentar uma tarefa para responder a problemas transitórios. Estas questões podem incluir erros de agendamento internos ou tentativas de recuperação de nós de computação em mau estado.|
|`requiredSlots`|Int32|As ranhuras necessárias para executar a tarefa.|
|[`nodeInfo`](#nodeInfo)|Tipo Complexo|Contém informações sobre o nó de computação em que a tarefa foi executada.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Tipo Complexo|Especifica que a tarefa é uma Tarefa multi-instância que requer múltiplos nós de computação.  Veja [`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task) mais detalhes.|
|[`constraints`](#constraints)|Tipo Complexo|Os constrangimentos de execução que se aplicam a esta tarefa.|
|[`schedulingError`](#schedulingError)|Tipo Complexo|Contém informações sobre o erro de agendamento da tarefa.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`poolId`|String|A identificação da piscina em que a tarefa funcionou.|
|`nodeId`|String|A identificação do nó em que a tarefa funcionou.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|O número de nós de computação exigidos pela tarefa.|

###  <a name="constraints"></a><a name="constraints"></a> constrangimentos

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|O número máximo de vezes que a tarefa pode ser novamente julgado. O serviço Batch retrição uma tarefa se o seu código de saída não for zero.<br /><br /> Note que este valor controla especificamente o número de retrações. O serviço Batch tentará a tarefa uma vez e poderá voltar a tentar até este limite. Por exemplo, se a contagem máxima de repetição for 3, Batch tenta uma tarefa até 4 vezes (uma tentativa inicial e 3 retrórias).<br /><br /> Se a contagem máxima de repetição for 0, o serviço Batch não relemca as tarefas.<br /><br /> Se a contagem máxima de repetição for de -1, o serviço de recauchutagem do Lote tarefas sem limite.<br /><br /> O valor predefinido é 0 (sem retrações).|


###  <a name="schedulingerror"></a><a name="schedulingError"></a> agendamentoError

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`category`|String|A categoria do erro.|
|`code`|String|Um identificador para o erro de agendamento de tarefas. Os códigos são invariantes e destinam-se a ser consumidos programáticamente.|
|`message`|String|Uma mensagem descrevendo o erro de agendamento de tarefas, destinada a ser exibida numa interface de utilizador.|
