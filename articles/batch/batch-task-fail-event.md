---
title: Evento de falha de tarefa de lote do Azure | Documentos da Microsoft
description: Referência para a tarefa de lote falhar eventos.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/15/2018
ms.author: v-junlch
ms.openlocfilehash: f37769ceb761b8c8bc4834568813bb1b7af7f66a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549993"
---
# <a name="task-fail-event"></a>Evento de falha de tarefa

 Este evento é emitido quando uma tarefa for concluída com uma falha. Atualmente, todos os códigos de saída diferente de zero são considerados falhas. Este evento será emitido *além* uma tarefa concluir evento e pode ser usado para detectar quando uma tarefa falhou.


 O exemplo seguinte mostra o corpo de uma tarefa falhar o evento.

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
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
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Nome do elemento|Type|Notas|
|------------------|----------|-----------|
|jobId|String|O id da tarefa que contém a tarefa.|
|ID|String|O id da tarefa.|
|taskType|String|O tipo da tarefa. Isso pode ser 'JobManager', que indica que é uma tarefa de gestão ou "User", que indica que não é uma tarefa de gestão. Este evento não é emitido para tarefas de preparação, tarefas de lançamento ou tarefas de início.|
|systemTaskVersion|Int32|Este é o contador de repetições interno numa tarefa. Internamente, o serviço Batch pode repetir uma tarefa para levar em conta problemas transitórios. Estes problemas podem incluir erros de agendamento internos ou tentativas para recuperar a partir de nós de computação num Estado incorreto.|
|[nodeInfo](#nodeInfo)|Tipo complexo|Contém informações sobre o nó de computação em que a tarefa foi executada.|
|[multiInstanceSettings](#multiInstanceSettings)|Tipo complexo|Especifica que a tarefa é uma tarefa de várias instâncias que requerem vários nós de computação.  Ver [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) para obter detalhes.|
|[Restrições](#constraints)|Tipo complexo|As restrições de execução que se aplicam a esta tarefa.|
|[executionInfo](#executionInfo)|Tipo complexo|Contém informações sobre a execução da tarefa.|

###  <a name="nodeInfo"></a> nodeInfo

|Nome do elemento|Type|Notas|
|------------------|----------|-----------|
|poolId|String|O id do conjunto no qual a tarefa foi executada.|
|nodeId|String|O id do nó no qual a tarefa foi executada.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Nome do elemento|Type|Notas|
|------------------|----------|-----------|
|numberOfInstances|Int32|O número de nós de computação de que a tarefa precisa.|

###  <a name="constraints"></a> Restrições

|Nome do elemento|Type|Notas|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|O número máximo de vezes que a tarefa pode ser repetida. O serviço Batch repete uma tarefa se o código de saída é diferente de zero.<br /><br /> Tenha em atenção que este valor controla especificamente o número de tentativas. O serviço Batch irá tentar a tarefa uma vez e, em seguida, pode voltar a tentar até esse limite. Por exemplo, se a contagem de repetições máxima for 3, tentativas de Batch uma tarefa até 4 vezes (uma tentativa inicial e 3 repetições).<br /><br /> Se a contagem de repetições máxima for 0, o serviço Batch não repete a ação tarefas.<br /><br /> Se a contagem de repetições máxima for -1, o serviço Batch repete tarefas sem limite.<br /><br /> O valor predefinido é 0 (não existem repetições).|


###  <a name="executionInfo"></a> executionInfo

|Nome do elemento|Type|Notas|
|------------------|----------|-----------|
|startTime|DateTime|A hora em que a tarefa iniciou a execução. 'Running' corresponde da **em execução** de estado, portanto, se a tarefa Especifica os arquivos de recursos ou pacotes de aplicações, em seguida, a hora de início reflete a hora em que a tarefa ao transferir ou implementar.  Se a tarefa foi reiniciada ou repetida, este é o tempo mais recente em que a tarefa iniciou a execução.|
|endTime|DateTime|A hora em que a tarefa concluída.|
|exitCode|Int32|O código de saída da tarefa.|
|retryCount|Int32|O número de vezes que a tarefa tiver sido repetida pelo serviço Batch. A tarefa é repetida se ele sairá com um código de saída diferente de zero, até o MaxTaskRetryCount especificado.|
|requeueCount|Int32|O número de vezes que a tarefa tem foi recolocado em fila pelo serviço Batch como resultado de um pedido de utilizador.<br /><br /> Quando o utilizador remove nós de um agrupamento (ao redimensionamento ou reduzir o conjunto) ou quando a tarefa está a ser desabilitada, o utilizador pode especificar que tarefas em nós de execução ser recolocadas para execução. Esta contagem controla quantas vezes a tarefa foi recolocado em fila por esses motivos.|

<!-- Update_Description: update metedata properties -->