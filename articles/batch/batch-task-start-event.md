---
title: Eventos de início de tarefa de lote do Azure | Documentos da Microsoft
description: Referência para o evento de início de tarefa do Batch.
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
ms.openlocfilehash: d50a0a7082e409084fd966370934a638ca9bb013
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549874"
---
# <a name="task-start-event"></a>Evento de início de tarefa

 Este evento é emitido quando uma tarefa foi agendada para iniciar num nó de computação pelo scheduler. Tenha em atenção que, se a tarefa é repetida ou recolocada este evento será novamente emitido para a mesma tarefa, mas a contagem de repetições e versão de tarefa do sistema será atualizado em conformidade.


 O exemplo seguinte mostra o corpo de uma tarefa Iniciar evento.

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
        "retryCount": 0
    }
}
```

|Nome do elemento|Type|Notas|
|------------------|----------|-----------|
|jobId|String|O id da tarefa que contém a tarefa.|
|ID|String|O id da tarefa.|
|taskType|String|O tipo da tarefa. Isso pode ser 'JobManager', que indica que é uma tarefa de gestão ou "User", que indica que não é uma tarefa de gestão.|
|systemTaskVersion|Int32|Este é o contador de repetições interno numa tarefa. Internamente, o serviço Batch pode repetir uma tarefa para levar em conta problemas transitórios. Estes problemas podem incluir erros de agendamento internos ou tentativas para recuperar a partir de nós de computação num Estado incorreto.|
|[nodeInfo](#nodeInfo)|Tipo complexo|Contém informações sobre o nó de computação em que a tarefa foi executada.|
|[multiInstanceSettings](#multiInstanceSettings)|Tipo complexo|Especifica que a tarefa é a necessidade de vários nós de computação de tarefa de várias instâncias.  Ver [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) para obter detalhes.|
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
|numberOfInstances|Int|O número de nós de computação de que a tarefa precisa.|

###  <a name="constraints"></a> Restrições

|Nome do elemento|Type|Notas|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|O número máximo de vezes que a tarefa pode ser repetida. O serviço Batch repete uma tarefa se o código de saída é diferente de zero.<br /><br /> Tenha em atenção que este valor controla especificamente o número de tentativas. O serviço Batch irá tentar a tarefa uma vez e, em seguida, pode voltar a tentar até esse limite. Por exemplo, se a contagem de repetições máxima for 3, tentativas de Batch uma tarefa até 4 vezes (uma tentativa inicial e 3 repetições).<br /><br /> Se a contagem de repetições máxima for 0, o serviço Batch não repete a ação tarefas.<br /><br /> Se a contagem de repetições máxima for -1, o serviço Batch repete tarefas sem limite.<br /><br /> O valor predefinido é 0 (não existem repetições).|

###  <a name="executionInfo"></a> executionInfo

|Nome do elemento|Type|Notas|
|------------------|----------|-----------|
|retryCount|Int32|O número de vezes que a tarefa tiver sido repetida pelo serviço Batch. A tarefa é repetida se ele sairá com um código de saída diferente de zero, até o MaxTaskRetryCount especificado|

<!-- Update_Description: update metedata properties -->