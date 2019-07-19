---
title: Evento de início de redimensionamento do pool do lote do Azure | Microsoft Docs
description: Referência do evento de início de redimensionamento do pool do lote.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: c7ef6bb9550b7398a10f5b57e6009d896256666b
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323181"
---
# <a name="pool-resize-start-event"></a>Evento de início de redimensionamento de conjunto

 Esse evento é emitido quando um redimensionamento de pool é iniciado. Como o redimensionamento do pool é um evento assíncrono, você pode esperar que um evento completo de redimensionamento do pool seja emitido após a conclusão da operação de redimensionamento.

 O exemplo a seguir mostra o corpo de um evento de início de redimensionamento de pool para um redimensionamento de pool de 0 a 2 nós com um redimensionamento manual.

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Elemento|Type|Notas|
|-------------|----------|-----------|
|poolid|Cadeia|A ID do pool.|
|nodeDeallocationOption|Cadeia|Especifica quando os nós podem ser removidos do pool, se o tamanho do pool estiver diminuindo.<br /><br /> Os valores possíveis são:<br /><br /> recolocar em **fila** – termine as tarefas em execução e recoloca-as na fila. As tarefas serão executadas novamente quando o trabalho for habilitado. Remova os nós assim que as tarefas forem encerradas.<br /><br /> **terminar** – encerrar tarefas em execução. As tarefas não serão executadas novamente. Remova os nós assim que as tarefas forem encerradas.<br /><br /> **taskcompletion** – permitir que as tarefas atualmente em execução sejam concluídas. Não agendar nenhuma nova tarefa enquanto aguarda. Remova os nós quando todas as tarefas tiverem sido concluídas.<br /><br /> **Retaineddata** – permitir que as tarefas atualmente em execução sejam concluídas e aguarde até que todos os períodos de retenção de dados da tarefa expirem. Não agendar nenhuma nova tarefa enquanto aguarda. Remova os nós quando todos os períodos de retenção de tarefa tiverem expirado.<br /><br /> O valor padrão é recolocar na fila.<br /><br /> Se o tamanho do pool estiver aumentando, o valor será definido como **inválido**.|
|currentDedicated|Int32|O número de nós de computação atribuídos ao pool no momento.|
|targetDedicated|Int32|O número de nós de computação que são solicitados para o pool.|
|enableAutoScale|Bool|Especifica se o tamanho do pool se ajusta automaticamente ao longo do tempo.|
|isAutoPool|Bool|Especifica se o pool foi criado por meio do mecanismo de autopool de um trabalho.|
