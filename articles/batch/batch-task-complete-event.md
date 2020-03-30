---
title: Evento completo da tarefa do Lote Azure
description: Referência para lote tarefa completo evento. Este evento é emitido uma vez que uma tarefa é concluída, independentemente do código de saída.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022924"
---
# <a name="task-complete-event"></a>Evento de conclusão de tarefa

 Este evento é emitido uma vez que uma tarefa é concluída, independentemente do código de saída. Este evento pode ser usado para determinar a duração de uma tarefa, para onde a tarefa foi decorreu, e se foi novamente julgado.


 O exemplo seguinte mostra o corpo de um evento completo de tarefas.

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
|`jobId`|Cadeia|A identificação do trabalho que contém a tarefa.|
|`id`|Cadeia|A identificação da tarefa.|
|`taskType`|Cadeia|O tipo de tarefa. Isto pode ser 'JobManager' indicando que se trata de uma tarefa de gestor de emprego ou de 'Utilizador' indicando que não é uma tarefa de gestor de emprego. Este evento não é emitido para tarefas de preparação de emprego, tarefas de libertação de emprego ou tarefas de início.|
|`systemTaskVersion`|Int32|Este é o contador de retry interno numa tarefa. Internamente, o serviço Batch pode voltar a tentar uma tarefa para responder a questões transitórias. Estas questões podem incluir erros de agendamento interno ou tentativas de recuperação de nóóis computacionais em mau estado.|
|[`nodeInfo`](#nodeInfo)|Tipo complexo|Contém informações sobre o nó computacional sobre o qual a tarefa funcionou.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Tipo complexo|Especifica que a tarefa é uma Tarefa multi-instância que requer vários nódosos de computação.  Veja [`multiInstanceSettings`](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) mais detalhes.|
|[`constraints`](#constraints)|Tipo complexo|Os constrangimentos de execução que se aplicam a esta tarefa.|
|[`executionInfo`](#executionInfo)|Tipo complexo|Contém informações sobre a execução da tarefa.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a>nodeInfo

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`poolId`|Cadeia|A identificação da piscina em que a tarefa correu.|
|`nodeId`|Cadeia|A identificação do nó em que a tarefa correu.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a>multiInstânciaSDefinições

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|O número de nós computacionais exigidos pela tarefa.|

###  <a name="constraints"></a><a name="constraints"></a>constrangimentos

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|O número máximo de vezes que a tarefa pode ser novamente tentada. O serviço 'Lote' retenta uma tarefa se o seu código de saída não for zero.<br /><br /> Note que este valor controla especificamente o número de repetições. O serviço Batch tentará a tarefa uma vez e poderá, em seguida, voltar a tentar até este limite. Por exemplo, se a contagem máxima de repetição for de 3, o Lote tenta uma tarefa até 4 vezes (uma tentativa inicial e 3 repetições).<br /><br /> Se a contagem máxima de retry for de 0, o serviço De lote não retry tarefas.<br /><br /> Se a contagem máxima de retry for -1, o serviço de lote retenta tarefas sem limite.<br /><br /> O valor predefinido é 0 (sem repetições).|

###  <a name="executioninfo"></a><a name="executionInfo"></a>execuçãoInfo

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`startTime`|DateTime|O tempo em que a tarefa começou a decorrer. 'Running' corresponde ao estado de **funcionamento,** pelo que se a tarefa especifica ficheiros de recursos ou pacotes de aplicações, então o tempo de início reflete o tempo em que a tarefa começou a descarregar ou a implantá-los.  Se a tarefa foi reiniciada ou novamente tentada, este é o momento mais recente em que a tarefa começou a decorrer.|
|`endTime`|DateTime|O tempo em que a tarefa foi concluída.|
|`exitCode`|Int32|O código de saída da tarefa.|
|`retryCount`|Int32|O número de vezes que a tarefa foi novamente tentada pelo serviço Batch. A tarefa é novamente tentada se sair com um código de saída não zero, até ao específico MaxTaskRetryCount.|
|`requeueCount`|Int32|O número de vezes que a tarefa foi requeued pelo serviço Batch como resultado de um pedido do utilizador.<br /><br /> Quando o utilizador remove os nós de uma piscina (redimensionando ou diminuindo a piscina) ou quando o trabalho está a ser desativado, o utilizador pode especificar que as tarefas de execução nos nós são requeidas para execução. Esta contagem acompanha quantas vezes a tarefa foi ressonada por estas razões.|
