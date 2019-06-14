---
title: Depurar tarefas recorrentes no Azure Data Lake Analytics
description: Saiba como utilizar o Azure Data Lake Tools para Visual Studio para depurar uma tarefa periódica anormal.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 5a2935d559a967151c5bdc01c4b0806fe52179b4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60629790"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Resolver problemas de uma tarefa periódica anormal

Este artigo mostra como usar [do Azure Data Lake Tools para Visual Studio](https://aka.ms/adltoolsvs) para resolver problemas relacionados com tarefas periódicas. Saiba mais sobre o pipeline e tarefas periódicas a partir da [blogue do Azure Data Lake e Azure HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Tarefas periódicas normalmente compartilham a mesma lógica de consulta e os dados de entrada semelhantes. Por exemplo, imagine que tem uma tarefa periódica em execução a cada manhã de segunda-feira às 8h00 para a contagem de utilizadores ativos de semanal da semana passada. Os scripts para estas tarefas partilham um modelo de script que contém a lógica de consulta. As entradas para estas tarefas são os dados de utilização para a última semana. Partilhar a mesma lógica de consulta e uma entrada semelhante, normalmente, significa que o desempenho destas tarefas é estável e semelhantes. Se um dos seus trabalhos recorrentes, de repente, executa anormalmente, falha ou muito mais lento, pode querer:

- Ver os relatórios de estatísticas para as execuções anteriores da tarefa periódica para ver o que aconteceu.
- Compare o trabalho anormal por um normal para descobrir o que foi alterado.

**Relacionados com a vista de tarefas** no Azure Data Lake Tools para Visual Studio ajuda que acelerar o progresso de resolução de problemas com ambos os casos.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Passo 1: Tarefas periódicas de localizar e abrir a vista de trabalho relacionados

Para utilizar a vista de tarefas relacionadas para resolver um problema de tarefa periódica, terá de primeiro localizar a tarefa periódica no Visual Studio e, em seguida, abra a vista de tarefas relacionadas.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Caso 1: Tem o URL para a tarefa periódica

Por meio **ferramentas** > **Data Lake** > **vista de tarefas**, pode colar o URL da tarefa para abrir a vista de tarefas no Visual Studio. Selecione **ver tarefas relacionadas** para abrir a vista de tarefas relacionadas.

![Ligação de trabalhos relacionados do Vista nas ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Caso 2: Tem o pipeline para a tarefa periódica, mas não o URL

No Visual Studio, é possível abrir o Browser de Pipeline através do Explorador de servidores > sua conta do Azure Data Lake Analytics > **Pipelines**. (Se não conseguir encontrar este nó no Explorador de servidores [baixar Plug mais recente-in](https://aka.ms/adltoolsvs).) 

![Selecionar o nó de Pipelines](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

No Browser do Pipeline, todos os pipelines para a conta do Data Lake Analytics são listados à esquerda. Pode expandir os pipelines para localizar todos os trabalhos recorrentes e, em seguida, selecione aquele que tem problemas. É aberta a vista de tarefas relacionadas à direita.

![Selecionar um pipeline e abrir a vista de trabalho relacionados](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Passo 2: Analisar um relatório de estatísticas

Um resumo e um relatório de estatísticas são apresentadas na parte superior da vista de tarefas relacionadas. Lá, pode encontrar a potencial causa do problema. 

1.  No relatório, o eixo x mostra o tempo de submissão de tarefa. Utilize-o para encontrar a tarefa anormal.
2.  Utilize o processo no diagrama seguinte para verificar as estatísticas e obter informações sobre o problema e possíveis soluções.

![Diagrama do processo para verificar as estatísticas](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Passo 3: Comparar o trabalho anormal a uma tarefa normal

Pode encontrar que todos submetido tarefas periódicas através da lista de tarefas na parte inferior da vista de tarefas relacionadas. Para obter mais informações e possíveis soluções, clique com botão direito a tarefa anormal. Utilize a vista de tarefa Diff para comparar o trabalho anormal por um normal anterior.

![Menu de atalho para comparar as tarefas](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Preste atenção para as grandes diferenças entre estas duas tarefas. Essas diferenças provavelmente estão a causar problemas de desempenho. Para verificar ainda mais, utilize os passos no diagrama seguinte:

![Diagrama do processo para verificar as diferenças entre tarefas](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Passos Seguintes

* [Resolver problemas de distorção de dados](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Depurar definidas pelo utilizador código c# para tarefas de U-SQL com falhas](data-lake-analytics-debug-u-sql-jobs.md)
