---
title: Evento completo da tarefa do Lote Azure
description: Referência para evento de conclusão de tarefa em lote. Esse evento é emitido quando uma tarefa é concluída, independentemente do código de saída.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 0fee5f071d5c7005e466bf4c3d0c1d0a7db24731
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022924"
---
# <a name="task-complete-event"></a>Evento de conclusão de tarefa

 Esse evento é emitido quando uma tarefa é concluída, independentemente do código de saída. Esse evento pode ser usado para determinar a duração de uma tarefa, onde a tarefa foi executada e se ela foi repetida.


 O exemplo a seguir mostra o corpo de um evento tarefa concluída.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
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
|`jobId`|Cadeia|A ID do trabalho que contém a tarefa.|
|`id`|Cadeia|A ID da tarefa.|
|`taskType`|Cadeia|O tipo da tarefa. Pode ser ' JobManager ' indicando que se trata de uma tarefa do Gerenciador de trabalho ou ' user ', indicando que ele não é uma tarefa do Gerenciador de trabalho. Esse evento não é emitido para tarefas de preparação de trabalho, tarefas de liberação de trabalho ou tarefas de inicialização.|
|`systemTaskVersion`|Int32|Este é o contador de repetição interno em uma tarefa. Internamente, o serviço de lote pode repetir uma tarefa para considerar problemas transitórios. Esses problemas podem incluir erros de agendamento internos ou tentativas de recuperação de nós de computação em um estado inadequado.|
|[`nodeInfo`](#nodeInfo)|Tipo complexo|Contém informações sobre o nó de computação no qual a tarefa foi executada.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Tipo complexo|Especifica que a tarefa é uma tarefa de várias instâncias que requer vários nós de computação.  Consulte [`multiInstanceSettings`](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) para obter detalhes.|
|[`constraints`](#constraints)|Tipo complexo|As restrições de execução que se aplicam a essa tarefa.|
|[`executionInfo`](#executionInfo)|Tipo complexo|Contém informações sobre a execução da tarefa.|

###  <a name="nodeInfo"></a>nodeInfo

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`poolId`|Cadeia|A ID do pool no qual a tarefa foi executada.|
|`nodeId`|Cadeia|A ID do nó no qual a tarefa foi executada.|

###  <a name="multiInstanceSettings"></a>multiInstanceSettings

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|O número de nós de computação exigidos pela tarefa.|

###  <a name="constraints"></a>reflexiva

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|O número máximo de vezes que a tarefa pode ser repetida. O serviço de lote repete uma tarefa se seu código de saída for diferente de zero.<br /><br /> Observe que esse valor controla especificamente o número de repetições. O serviço de lote tentará executar a tarefa uma vez e, em seguida, poderá tentar novamente até esse limite. Por exemplo, se a contagem máxima de repetições for 3, o lote tentará uma tarefa até quatro vezes (uma tentativa inicial e 3 repetições).<br /><br /> Se a contagem máxima de repetições for 0, o serviço de lote não repetirá as tarefas.<br /><br /> Se a contagem máxima de repetições for-1, o serviço de lote tentará novamente as tarefas sem limite.<br /><br /> O valor padrão é 0 (sem repetições).|

###  <a name="executionInfo"></a>executionInfo

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`startTime`|DateTime|A hora em que a tarefa começou a ser executada. ' Running ' corresponde ao estado de **execução** , portanto, se a tarefa especificar arquivos de recursos ou pacotes de aplicativos, a hora de início refletirá a hora em que a tarefa começou a baixar ou a implantar.  Se a tarefa foi reiniciada ou tentada novamente, essa é a hora mais recente na qual a tarefa começou a ser executada.|
|`endTime`|DateTime|A hora em que a tarefa foi concluída.|
|`exitCode`|Int32|O código de saída da tarefa.|
|`retryCount`|Int32|O número de vezes que a tarefa foi repetida pelo serviço de lote. A tarefa será repetida se for encerrada com um código de saída diferente de zero, até o MaxTaskRetryCount especificado.|
|`requeueCount`|Int32|O número de vezes que a tarefa foi recolocada na fila pelo serviço de lote como resultado de uma solicitação de usuário.<br /><br /> Quando o usuário remove nós de um pool (redimensionando ou reduzindo o pool) ou quando o trabalho está sendo desabilitado, o usuário pode especificar que as tarefas em execução nos nós sejam recolocadas na fila para execução. Essa contagem controla quantas vezes a tarefa foi recolocada em fila por esses motivos.|
