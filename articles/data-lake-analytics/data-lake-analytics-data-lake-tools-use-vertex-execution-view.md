---
title: Vertex Execution View em Ferramentas do Lago de Dados para Estúdio Visual
description: Este artigo descreve como usar a Visão de Execução vertex para fazer o exame de data lake analytics trabalhos.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: f5adbb75e6852551976aa040a1a1c723d2e3f59b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "71309717"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Use a vista de execução de vértice em ferramentas do lago de dados para estúdio visual
Saiba como usar a Vista de Execução Vertex para fazer o exame de data lake analytics.


## <a name="open-the-vertex-execution-view"></a>Abra a vista de execução do Vértice
Abra um trabalho U-SQL em Ferramentas do Lago de Dados para Estúdio Visual. Clique na **Verção de Execução vertex** no canto inferior esquerdo. Pode ser solicitado a carregar primeiro os perfis e pode demorar algum tempo dependendo da conectividade da sua rede.

![Data Lake Analytics Ferramentas Vertex Execução Vista](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Compreender a Visão de Execução de Vértice
A Vista de Execução vertex tem três partes:

![Data Lake Analytics Ferramentas Vertex Execução Vista](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

O **seletor Vertex** à esquerda permite selecionar vértices por funcionalidades (como a leitura dos 10 melhores dados ou escolher por fase). Um dos filtros mais utilizados é ver os **vértices no caminho crítico.** O **caminho crítico** é a mais longa cadeia de vértices de um trabalho U-SQL. Compreender o caminho crítico é útil para otimizar os seus empregos, verificando qual o vértice que demora mais tempo.
  
![Data Lake Analytics Ferramentas Vertex Execução Vista](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

O painel central superior mostra o **estado de funcionamento de todos os vértices**.
  
![Data Lake Analytics Ferramentas Vertex Execução Vista](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

O painel central inferior mostra informações sobre cada vértice:
* Nome do processo: O nome da instância do vértice. É composto por diferentes partes no StageName. VertexName VertexRunInstance. Por exemplo, o SV7_Split[62].v1 vértice representa a segunda instância em execução (.v1, índice a partir de 0) do Vertex número 62 na fase SV7_Split.
* Total de Leitura/Escrita: Os dados foram lidos/escritos por este vértice.
* Estado/Estado de saída: O estado final quando o vértice estiver terminado.
* Código de saída/Tipo de Falha: Erro quando o vértice falhou.
* Razão da Criação: Por que o vértice foi criado.
* Latência/Processo de Recursos/PN Fila: o tempo que o vértice demorou a esperar pelos recursos, a processar dados e a permanecer na fila.
* Processo/Criador GUID: GUID para o vértice de execução atual ou o seu criador.
* Versão: a n-th instância do vértice em execução (o sistema pode agendar novas instâncias de um vértice por muitas razões, por exemplo, failover, redundância computacional, etc.)
* Versão Criou o Tempo.
* Processo Criar tempo de início/processo Tempo/Processo Tempo de início/Processo Tempo de início/Tempo completo processo: quando o processo do vértice começar a criar; quando o processo do vértice começa a fazer fila; quando o processo de certos vértices começar; quando o certo vértice estiver concluído.

## <a name="next-steps"></a>Passos seguintes
* Para obter informações de diagnóstico de registo, veja [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para ver detalhes do trabalho, consulte [use Job Browser e Job View for Azure Data lake Analytics jobs](data-lake-analytics-data-lake-tools-view-jobs.md)
