---
title: Vertex Execução vista em ferramentas de lago de dados para estúdio visual
description: Este artigo descreve como usar a Vertex Execution View para analisar trabalhos de Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: f5adbb75e6852551976aa040a1a1c723d2e3f59b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309717"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Use a vista de execução vertex em ferramentas de lago de dados para estúdio visual
Aprenda a usar a Vertex Execution View para analisar trabalhos de Data Lake Analytics.


## <a name="open-the-vertex-execution-view"></a>Abra a Vista de Execução do Vértice
Abra um trabalho u-SQL em Data Lake Tools for Visual Studio. Clique na **vertex execução Ver** no canto inferior esquerdo. Pode ser solicitado a carregar os perfis primeiro e pode demorar algum tempo dependendo da conectividade da sua rede.

![Data Lake Analytics Tools Vertex Execution View](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Compreender a vista de execução do Vertex
A Vertex Execution View tem três partes:

![Data Lake Analytics Tools Vertex Execution View](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

O **seletor Vertex** à esquerda permite selecionar vértices por funcionalidades (como ler os 10 melhores dados ou escolher por etapa). Um dos filtros mais utilizados é ver os **vértices no caminho crítico**. O **caminho crítico** é a mais longa cadeia de vértices de um trabalho u-SQL. Compreender o caminho crítico é útil para otimizar os seus trabalhos verificando qual o vértice que demora mais tempo.
  
![Data Lake Analytics Tools Vertex Execution View](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

O painel central superior mostra o **estado de funcionamento de todos os vértices**.
  
![Data Lake Analytics Tools Vertex Execution View](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

O painel inferior central mostra informações sobre cada vértice:
* Nome do processo: O nome da instância do vértice. É composto por diferentes partes no StageName. VertexName. VertexRunInstance. Por exemplo, o vertex SV7_Split[62].v1 significa a segunda instância de execução (.v1, índice a partir de 0) do vertex número 62 na fase SV7_Split.
* Leitura total de dados/escrito: Os dados foram lidos/escritos por este vértice.
* Estado/Saída: O estado final quando o vértice terminar.
* Tipo de código/falha de saída: O erro quando o vértice falhou.
* Razão da Criação: Por que o vértice foi criado.
* Latência de Recursos/Latência de Fila de Processo/PN: o tempo necessário para que o vértice aguarde os recursos, processe dados e permaneça na fila.
* Processo/Criador GUID: GUID para o vértice de execução atual ou o seu criador.
* Versão: a instância N-th do vértice de execução (o sistema pode agendar novos casos de um vértice por muitas razões, por exemplo falha, redundância do cálculo, etc.)
* Versão Criou o Tempo.
* Processo Criar Tempo de Início/Processo De Tempo/Processo Prazo/Processo Tempo Completo: quando o processo de vértice iniciar a criação; quando o processo de vértice começar a fazer fila; quando o processo de vértice determinado começar; quando o certo vértice estiver concluído.

## <a name="next-steps"></a>Passos seguintes
* Para obter informações de diagnóstico de registo, veja [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para ver detalhes de trabalho, consulte [Use Job Browser e Job View para trabalhos de Análise](data-lake-analytics-data-lake-tools-view-jobs.md) do lago de dados Azure
