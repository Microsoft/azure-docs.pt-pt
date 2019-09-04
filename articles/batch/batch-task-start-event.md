---
title: Evento de início de tarefa do lote do Azure | Microsoft Docs
description: Referência do evento de início de tarefa de lote.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: ffad1696bc2c85a1a150ac87d90c2fb9c34e1519
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258532"
---
# <a name="task-start-event"></a>Evento de início de tarefa

 Esse evento é emitido depois que uma tarefa é agendada para ser iniciada em um nó de computação pelo Agendador. Observe que, se a tarefa for repetida ou colocada novamente na fila, esse evento será emitido novamente para a mesma tarefa, mas a contagem de repetição e a versão da tarefa do sistema serão atualizadas de acordo.


 O exemplo a seguir mostra o corpo de um evento de início de tarefa.

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

|Nome do elemento|Type|Notas|
|------------------|----------|-----------|
|`jobId`|Cadeia|A ID do trabalho que contém a tarefa.|
|`id`|Cadeia|A ID da tarefa.|
|`taskType`|Cadeia|O tipo da tarefa. Pode ser ' JobManager ' indicando que se trata de uma tarefa do Gerenciador de trabalho ou ' user ', indicando que ele não é uma tarefa do Gerenciador de trabalho.|
|`systemTaskVersion`|Int32|Este é o contador de repetição interno em uma tarefa. Internamente, o serviço de lote pode repetir uma tarefa para considerar problemas transitórios. Esses problemas podem incluir erros de agendamento internos ou tentativas de recuperação de nós de computação em um estado inadequado.|
|[`nodeInfo`](#nodeInfo)|Tipo complexo|Contém informações sobre o nó de computação no qual a tarefa foi executada.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Tipo complexo|Especifica que a tarefa é uma tarefa de várias instâncias que requer vários nós de computação.  Consulte [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) para obter detalhes.|
|[`constraints`](#constraints)|Tipo complexo|As restrições de execução que se aplicam a essa tarefa.|
|[`executionInfo`](#executionInfo)|Tipo complexo|Contém informações sobre a execução da tarefa.|

###  <a name="nodeInfo"></a>nodeInfo

|Nome do elemento|Type|Notas|
|------------------|----------|-----------|
|`poolId`|Cadeia|A ID do pool no qual a tarefa foi executada.|
|`nodeId`|Cadeia|A ID do nó no qual a tarefa foi executada.|

###  <a name="multiInstanceSettings"></a>multiInstanceSettings

|Nome do elemento|Type|Notas|
|------------------|----------|-----------|
|`numberOfInstances`|Int|O número de nós de computação exigidos pela tarefa.|

###  <a name="constraints"></a>reflexiva

|Nome do elemento|Type|Notas|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|O número máximo de vezes que a tarefa pode ser repetida. O serviço de lote repete uma tarefa se seu código de saída for diferente de zero.<br /><br /> Observe que esse valor controla especificamente o número de repetições. O serviço de lote tentará executar a tarefa uma vez e, em seguida, poderá tentar novamente até esse limite. Por exemplo, se a contagem máxima de repetições for 3, o lote tentará uma tarefa até quatro vezes (uma tentativa inicial e 3 repetições).<br /><br /> Se a contagem máxima de repetições for 0, o serviço de lote não repetirá as tarefas.<br /><br /> Se a contagem máxima de repetições for-1, o serviço de lote tentará novamente as tarefas sem limite.<br /><br /> O valor padrão é 0 (sem repetições).|

###  <a name="executionInfo"></a>executionInfo

|Nome do elemento|Type|Notas|
|------------------|----------|-----------|
|`retryCount`|Int32|O número de vezes que a tarefa foi repetida pelo serviço de lote. A tarefa será repetida se for encerrada com um código de saída diferente de zero, até o MaxTaskRetryCount especificado|
