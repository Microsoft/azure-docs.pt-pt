---
title: Evento de início de tarefa do Lote Azure
description: Informações de referência para evento de início de tarefa de Lote. Este evento é emitido uma vez que uma tarefa está programada para começar em um nó de computação pelo programador.
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
ms.openlocfilehash: bed3749e29867298f3e8258a08448b7b094055ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022822"
---
# <a name="task-start-event"></a>Evento de início de tarefa

 Este evento é emitido uma vez que uma tarefa está programada para começar em um nó de computação pelo programador. Note que se a tarefa for novamente experimentada ou ressued este evento será novamente emitido para a mesma tarefa, mas a contagem de volta e a versão de tarefa do sistema serão atualizadas em conformidade.


 O exemplo que se segue mostra o corpo de um evento de início de tarefa.

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
|`jobId`|Cadeia|A identificação do trabalho que contém a tarefa.|
|`id`|Cadeia|A identificação da tarefa.|
|`taskType`|Cadeia|O tipo de tarefa. Isto pode ser 'JobManager' indicando que se trata de uma tarefa de gestor de emprego ou de 'Utilizador' indicando que não é uma tarefa de gestor de emprego.|
|`systemTaskVersion`|Int32|Este é o contador de retry interno numa tarefa. Internamente, o serviço Batch pode voltar a tentar uma tarefa para responder a questões transitórias. Estas questões podem incluir erros de agendamento interno ou tentativas de recuperação de nóóis computacionais em mau estado.|
|[`nodeInfo`](#nodeInfo)|Tipo complexo|Contém informações sobre o nó computacional sobre o qual a tarefa funcionou.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Tipo complexo|Especifica que a tarefa é tarefa multi-instância que requer vários nódosos de computação.  Consulte [várias Definições](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) de Instâncias para obter detalhes.|
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
|`numberOfInstances`|int|O número de nós computacionais exigidos pela tarefa.|

###  <a name="constraints"></a><a name="constraints"></a>constrangimentos

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|O número máximo de vezes que a tarefa pode ser novamente tentada. O serviço 'Lote' retenta uma tarefa se o seu código de saída não for zero.<br /><br /> Note que este valor controla especificamente o número de repetições. O serviço Batch tentará a tarefa uma vez e poderá, em seguida, voltar a tentar até este limite. Por exemplo, se a contagem máxima de repetição for de 3, o Lote tenta uma tarefa até 4 vezes (uma tentativa inicial e 3 repetições).<br /><br /> Se a contagem máxima de retry for de 0, o serviço De lote não retry tarefas.<br /><br /> Se a contagem máxima de retry for -1, o serviço de lote retenta tarefas sem limite.<br /><br /> O valor predefinido é 0 (sem repetições).|

###  <a name="executioninfo"></a><a name="executionInfo"></a>execuçãoInfo

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`retryCount`|Int32|O número de vezes que a tarefa foi novamente tentada pelo serviço Batch. A tarefa é novamente tentada se sair com um código de saída não zero, até ao específico MaxTaskRetryCount|
