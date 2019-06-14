---
title: Utilizar a vista de execução de vértice no Data Lake Tools para Visual Studio
description: Este artigo descreve como utilizar a vista de execução de vértice para tarefas de Data Lake Analytics de exame.
services: data-lake-analytics
ms.service: data-lake-analytics
author: mumian
ms.author: jgao
ms.reviewer: jasonwhowell
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: 73314c5864e3036d102deee2792021345b80bf2e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60687837"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Utilizar a vista de execução de vértice no Data Lake Tools para Visual Studio
Saiba como utilizar a vista de execução de vértice para tarefas de Data Lake Analytics de exame.


## <a name="open-the-vertex-execution-view"></a>Abrir a vista de execução do vértice
Abra uma tarefa U-SQL no Data Lake Tools para Visual Studio. Clique em **vista de execução de vértice** no canto inferior esquerdo. Poderá ser solicitado para perfis de carga em primeiro lugar e pode demorar algum tempo consoante a conectividade da rede.

![Vista de execução de vértice de ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Compreender a vista de execução do vértice
A vista de execução de vértice possui três partes:

![Vista de execução de vértice de ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

O **Seletor de vértice** no esquerdo permite selecionar vértices pelos recursos (como top 10 dados de leitura ou optar por fase). Um dos filtros mais utilizados é ver os **vértices no caminho crítico**. O **caminho crítico** é a mais longa cadeia de vértices de uma tarefa de U-SQL. Noções básicas sobre o caminho crítico é útil para otimizar as suas tarefas, verificando qual vértice demora muito tempo.
  
![Vista de execução de vértice de ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Mostra de painel a parte superior ao centro do **com o estado de todos os vértices**.
  
![Vista de execução de vértice de ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Painel central na parte inferior mostra informações sobre cada vértice:
* Nome do processo: O nome da instância do vértice. Ele é composto por diferentes partes no StageName | VertexName | VertexRunInstance. Por exemplo, o vértice de .v1 [62] SV7_Split representa a instância em execução segundo (.v1, índice a partir de 0) do número de vértice 62 no estágio SV7_Split.
* Total de dados lidos/escritos: Os dados foram lidos/escritos por esta vértice.
* Estado/sair do Estado: O estado final quando o vértice é terminado.
* Tipo de falha do código de saída: O erro quando o vértice falhou.
* Razão de criação: Por que o vértice foi criado.
* Latência de fila de latência/PN de latência/processos de recursos: o tempo necessário para o vértice de esperar por recursos, para processar dados e para se manter na fila.
* GUID de processo/criador: GUID do vértice em execução atual ou o seu criador.
* Versão: a N-th instância do vértice em execução (o sistema poderá agendar novas instâncias de um vértice para computação de muitos motivos pelos quais, por exemplo, ativação pós-falha, redundância, etc.)
* Versão criada a tempo.
* Criar início tempo/processo em fila tempo/processo início tempo/processo concluído de processar tempo: quando o processo de vértice começa a criar; Quando o processo de vértice é iniciado para a fila; Quando é iniciado o processo de vértice determinados; Quando a determinados vértice está concluída.

## <a name="next-steps"></a>Passos Seguintes
* Para obter informações de diagnóstico de registo, veja [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para ver os detalhes da tarefa, consulte [utilizar o Browser de trabalho e a vista de tarefas para tarefas de Azure Data lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
