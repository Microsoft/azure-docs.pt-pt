---
title: Exibição de execução de vértice no Data Lake Tools para Visual Studio
description: Este artigo descreve como usar o modo de exibição de execução de vértice para fazer exames Data Lake Analytics trabalhos.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: f5adbb75e6852551976aa040a1a1c723d2e3f59b
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309717"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Usar o modo de exibição de execução de vértice no Data Lake Tools para Visual Studio
Saiba como usar o modo de exibição de execução de vértice para fazer exames Data Lake Analytics trabalhos.


## <a name="open-the-vertex-execution-view"></a>Abrir o modo de exibição de execução de vértice
Abra um trabalho do U-SQL no Data Lake Tools para Visual Studio. Clique em **exibição de execução de vértice** no canto inferior esquerdo. Você pode ser solicitado a carregar os perfis primeiro e pode levar algum tempo, dependendo da conectividade de sua rede.

![Exibição de execução de vértice de ferramentas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Entender a exibição de execução de vértice
O modo de exibição de execução de vértice tem três partes:

![Exibição de execução de vértice de ferramentas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

O **seletor de vértice** à esquerda permite que você selecione vértices por recursos (como 10 principais dados lidos ou escolha por estágio). Um dos filtros mais comumente usados é Ver os **vértices em um caminho crítico**. O **caminho crítico** é a cadeia mais longa de vértices de um trabalho do U-SQL. Entender o caminho crítico é útil para otimizar seus trabalhos verificando qual vértice leva o tempo mais longo.
  
![Exibição de execução de vértice de ferramentas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

O painel central superior mostra o **status de execução de todos os vértices**.
  
![Exibição de execução de vértice de ferramentas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

O painel central inferior mostra informações sobre cada vértice:
* Nome do processo: O nome da instância de vértice. Ele é composto de diferentes partes em Stagename | Vérticename | VertexRunInstance. Por exemplo, o vértice SV7_Split [62]. v1 significa a segunda instância em execução (. v1, índice a partir de 0) do número de vértice 62 no estágio SV7_Split.
* Total de dados lidos/gravados: Os dados foram lidos/gravados por este vértice.
* Status de estado/saída: O status final quando o vértice é finalizado.
* Código de saída/tipo de falha: O erro quando o vértice falhou.
* Motivo da criação: Por que o vértice foi criado.
* Latência do recurso/latência do processo/latência da fila de PN: o tempo levado para o vértice aguardar os recursos, processar dados e permanecer na fila.
* GUID do processo/Criador: GUID para o vértice em execução atual ou seu criador.
* Versão: a instância N-th do vértice em execução (o sistema pode agendar novas instâncias de um vértice por vários motivos, por exemplo, failover, redundância de computação, etc.)
* Hora da criação da versão.
* Processo criar hora de início/processo na fila/hora de início do processo/tempo de execução concluído: quando o processo de vértice inicia a criação; Quando o processo de vértice começa a entrar na fila; Quando o processo de determinado vértice é iniciado; Quando o determinado vértice for concluído.

## <a name="next-steps"></a>Passos seguintes
* Para obter informações de diagnóstico de registo, veja [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para exibir os detalhes do trabalho, consulte [usar o navegador de trabalho e a exibição de trabalho para trabalhos do Azure data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
