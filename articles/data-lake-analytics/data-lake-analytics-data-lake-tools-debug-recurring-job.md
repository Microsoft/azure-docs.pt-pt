---
title: Depurar empregos recorrentes no Azure Data Lake Analytics
description: Aprenda a usar ferramentas de lago de dados Azure para o Estúdio Visual para depurar um trabalho recorrente anormal.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 5a2935d559a967151c5bdc01c4b0806fe52179b4
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60629790"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Resolver problemas de uma tarefa periódica anormal

Este artigo mostra como usar ferramentas de lago de [dados Azure para estúdio visual para](https://aka.ms/adltoolsvs) resolver problemas com empregos recorrentes. Saiba mais sobre o oleoduto e os empregos recorrentes do [Azure Data Lake e do blog Azure HDInsight.](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/)

Os empregos recorrentes geralmente partilham a mesma lógica de consulta e dados de entrada semelhantes. Por exemplo, imagine que tem um emprego recorrente a funcionar todas as segundas-feiras de manhã às 8 da manhã. para contar o utilizador ativo semanal da semana passada. Os scripts para estes trabalhos partilham um modelo de script que contém a lógica da consulta. Os contributos para estes trabalhos são os dados de utilização da semana passada. Partilhar a mesma lógica de consulta e contributos semelhantes geralmente significa que o desempenho destes empregos é semelhante e estável. Se um dos seus trabalhos recorrentes de repente se apresentar de forma anormal, falhar ou abrandar muito, talvez queira:

- Consulte os relatórios estatísticos relativos aos anteriores trabalhos recorrentes para ver o que aconteceu.
- Compare o trabalho anormal com um normal para descobrir o que foi mudado.

**RelacionadoJob View** in Azure Data Lake Tools for Visual Studio ajuda-o a acelerar o progresso de resolução de problemas com ambos os casos.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Passo 1: Encontrar empregos recorrentes e abrir vista de emprego relacionada

Para usar a Related Job View para resolver um problema de trabalho recorrente, precisa primeiro de encontrar o emprego recorrente no Visual Studio e, em seguida, abrir related Job View.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Caso 1: Você tem o URL para o trabalho recorrente

Através de **Ferramentas** > **Data Lake** > **Job View,** você pode colar o URL de trabalho para abrir a Vista de Emprego em Estúdio Visual. Selecione **Ver Empregos Relacionados** para abrir vista de emprego relacionada.

![Ver link de empregos relacionados em ferramentas de análise de data lake](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Caso 2: Você tem o oleoduto para o trabalho recorrente, mas não o URL

No Estúdio Visual, pode abrir o Pipeline Browser através do Server Explorer > a sua conta Azure Data Lake Analytics > **Pipelines**. (Se não encontrar este nó no Server Explorer, [faça o download do mais recente plug-in](https://aka.ms/adltoolsvs).) 

![Selecionando o nó pipelines](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

No Pipeline Browser, todos os oleodutos para a conta Data Lake Analytics estão listados à esquerda. Você pode expandir os oleodutos para encontrar todos os empregos recorrentes, e depois selecionar aquele que tem problemas. Vista de Emprego Relacionada abre à direita.

![Selecionando um pipeline e abertura de vista de trabalho relacionada](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Passo 2: Analisar um relatório estatístico

Um resumo e um relatório estatístico são apresentados no topo da Visão de Emprego Relacionada. Aí, pode encontrar a potencial causa do problema. 

1.  No relatório, o eixo X mostra o tempo de submissão do trabalho. Usa-o para encontrar o trabalho anormal.
2.  Utilize o processo no diagrama seguinte para verificar as estatísticas e obter informações sobre o problema e as possíveis soluções.

![Diagrama de processo para verificar estatísticas](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Passo 3: Compare o trabalho anormal com um trabalho normal

Você pode encontrar todos os trabalhos recorrentes submetidos através da lista de trabalho no fundo da Related Job View. Para encontrar mais insights e soluções potenciais, clique no trabalho anormal. Utilize a vista Job Diff para comparar o trabalho anormal com um trabalho normal anterior.

![Menu de atalho para comparar empregos](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Preste atenção às grandes diferenças entre estes dois empregos. Essas diferenças estão provavelmente a causar os problemas de desempenho. Para verificar mais, utilize os passos no seguinte diagrama:

![Diagrama de processo para verificar diferenças entre empregos](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas de distorção de dados](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Código C# definido pelo utilizador para empregos falhados da U-SQL](data-lake-analytics-debug-u-sql-jobs.md)
