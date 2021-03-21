---
title: Debug empregos recorrentes no Azure Data Lake Analytics
description: Aprenda a usar ferramentas do Azure Data Lake para o Estúdio Visual para depurar um trabalho anormal e recorrente.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 05/20/2018
ms.openlocfilehash: b95d4b0a082e8eac191d610c9516e5ce72180b4c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219178"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Resolver problemas de uma tarefa periódica anormal

Este artigo mostra como usar [a Azure Data Lake Tools para o Visual Studio](https://aka.ms/adltoolsvs) para resolver problemas com empregos recorrentes. Saiba mais sobre o oleoduto e os empregos recorrentes a partir do [Azure Data Lake e do blog Azure HDInsight.](/archive/blogs/azuredatalake/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy)

Os empregos recorrentes geralmente partilham a mesma lógica de consulta e dados de entrada semelhantes. Por exemplo, imagine que tem um emprego recorrente a funcionar todas as segundas-feiras de manhã às 8:00.M. para contar o utilizador semanal ativo da semana passada. Os scripts para estes trabalhos partilham um modelo de script que contém a lógica de consulta. Os inputs para estes trabalhos são os dados de utilização da semana passada. Partilhar a mesma lógica de consulta e contributos semelhantes geralmente significa que o desempenho destes trabalhos é semelhante e estável. Se um dos seus trabalhos recorrentes de repente funcionar de forma anormal, falhar ou abrandar muito, talvez queira:

- Consulte os relatórios estatísticos das séries anteriores do trabalho recorrente para ver o que aconteceu.
- Compare o trabalho anormal com um normal para descobrir o que foi mudado.

**Relacionados Job View** in Azure Data Lake Tools for Visual Studio ajuda-o a acelerar o progresso na resolução de problemas com ambos os casos.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Passo 1: Encontrar empregos recorrentes e abrir vista de emprego relacionada

Para utilizar a Vista de Emprego Relacionada para resolver um problema de trabalho recorrente, é necessário primeiro encontrar o emprego recorrente no Visual Studio e depois abrir a Related Job View.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Caso 1: Você tem a URL para o trabalho recorrente

Através de **Tools**  >  **Data Lake** Job  >  **View,** pode colar o URL de trabalho para abrir a Job View no Estúdio Visual. Selecione **Ver Empregos Relacionados** para abrir a vista de trabalho relacionada.

![Ver links de empregos relacionados em ferramentas de análise de lago de dados](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Caso 2: Você tem o oleoduto para o trabalho recorrente, mas não o URL

No Visual Studio, pode abrir o Pipeline Browser através do Server Explorer > a sua conta Azure Data Lake Analytics > **Pipelines**. (Se não conseguir encontrar este nó no Server Explorer, [descarregue o mais recente plug-in](https://aka.ms/adltoolsvs).) 

![Selecionando o nó pipelines](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

No Pipeline Browser, todos os oleodutos para a conta Data Lake Analytics estão listados à esquerda. Pode expandir os oleodutos para encontrar todos os empregos recorrentes e, em seguida, selecionar o que tem problemas. Relacionados Job View abre à direita.

![Selecionando um oleoduto e abrindo a visão de trabalho relacionada](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Passo 2: Analisar um relatório estatístico

Um resumo e um relatório estatístico são apresentados no topo da visão de emprego relacionada. Lá, pode encontrar a potencial causa do problema. 

1.  No relatório, o eixo X mostra o tempo de submissão do trabalho. Usa-o para encontrar o trabalho anormal.
2.  Utilize o processo no diagrama seguinte para verificar estatísticas e obter informações sobre o problema e as soluções possíveis.

![Diagrama de processo para verificação de estatísticas](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Passo 3: Compare o trabalho anormal com um trabalho normal

Você pode encontrar todos os trabalhos recorrentes submetidos através da lista de emprego na parte inferior da Vista de Emprego Relacionada. Para encontrar mais insights e soluções potenciais, clique com o botão direito no trabalho anormal. Utilize a vista Job Diff para comparar o trabalho anormal com o anterior normal.

![Menu de atalho para comparar empregos](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Preste atenção às grandes diferenças entre estes dois empregos. Essas diferenças estão provavelmente a causar os problemas de desempenho. Para verificar mais aprofundadamente, utilize os passos no seguinte diagrama:

![Diagrama de processo para verificar diferenças entre postos de trabalho](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas de distorção de dados](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Código C# definido pelo utilizador para trabalhos falhados de U-SQL](data-lake-analytics-debug-u-sql-jobs.md)