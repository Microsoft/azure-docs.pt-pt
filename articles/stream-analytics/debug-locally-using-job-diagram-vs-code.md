---
title: Debug Azure Stream Analytics consulta localmente usando diagrama de trabalho em Código de Estúdio Visual
description: Este artigo descreve como depurar consultas localmente usando o diagrama de trabalho na extensão Azure Stream Analytics para Código de Estúdio Visual.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 25ad696ad345fbf672f6bf26eb3f35a13fb03ea5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019504"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>Debug Azure Stream Analytics consulta localmente usando diagrama de trabalho em Código de Estúdio Visual

Os trabalhos de streaming que não resultam nem resultados inesperados precisam frequentemente de resolução de problemas. A extensão visual do Código do Estúdio para Azure Stream Analytics integra diagramas de trabalho, métricas, registos de diagnóstico e resultados intermédios para ajudá-lo a isolar rapidamente a origem de um problema. Pode utilizar o diagrama de trabalho enquanto testa a sua consulta localmente para examinar o conjunto de resultados intermédios e as métricas para cada passo.

## <a name="debug-a-query-using-job-diagram"></a>Depurar uma consulta usando o diagrama de trabalho

Um script Azure Stream Analytics é usado para transformar dados de entrada para dados de saída. O diagrama de trabalho mostra como os dados fluem de fontes de entrada, como o Event Hub ou o IoT Hub, através de múltiplos passos de consulta para os lavatórios de saída. Cada passo de consulta é mapeado para um conjunto de resultados temporário definido no script usando uma `WITH` declaração. Pode ver os dados, bem como as métricas de cada passo de consulta em cada conjunto de resultados intermédios para encontrar a origem de um problema.

> [!NOTE]
> Este diagrama de trabalho apenas mostra os dados e métricas para testes locais num único nó. Não deve ser utilizado para afinação de desempenho e resolução de problemas.

### <a name="start-local-testing"></a>Iniciar testes locais

Utilize este [Quickstart](quick-create-visual-studio-code.md) para aprender a criar um trabalho stream Analytics usando o Código de Estúdio Visual ou [exporte um emprego existente para um projeto local.](visual-studio-code-explore-jobs.md) As credenciais para entradas e saídas são automaticamente povoadas para empregos exportados.

Se pretender testar a consulta com os dados de entrada locais, siga estas [instruções](visual-studio-code-local-run.md). Se quiser testar com entrada ao vivo, [configure a sua entrada](stream-analytics-add-inputs.md) para o passo seguinte. 

Abra o ficheiro de script *\. asaql* e selecione **Executar localmente**. Em seguida, **selecione Use local input** ou Use a entrada ao **vivo**. O diagrama de trabalho aparece no lado direito da janela.

### <a name="view-the-output-and-intermediate-result-set"></a>Ver a saída e o conjunto de resultados intermédios  

1. Todas as saídas de trabalho são apresentadas na janela de resultados no lado inferior direito da janela Visual Studio Code.

   > [!div class="mx-imgBorder"]
   > ![Resultados da saída de emprego](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. Selecione o passo de consulta para navegar para o script. É automaticamente direcionado para o script correspondente no editor à esquerda. O resultado intermédio aparece na janela de resultados no lado inferior direito da janela Visual Studio Code.

   > [!div class="mx-imgBorder"]
   > ![Resultado da pré-visualização do diagrama de trabalho](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>Ver métricas

Nesta secção, você explora as métricas disponíveis para cada parte do diagrama.

1. Selecione o **separador Métricas** ao lado do **separador Resultados** no lado inferior direito da janela Código do Estúdio Visual.

2. Selecione **Job** a partir do dropdown. Pode selecionar qualquer espaço vazio num nó gráfico para navegar para métricas de nível de trabalho. Esta vista contém todas as métricas, que são atualizadas a cada 10 segundos quando o trabalho está em funcionamento. Pode selecionar ou desmarcar as métricas do lado direito para as visualizar nas tabelas.

   > [!div class="mx-imgBorder"]
   > ![Métricas do diagrama de trabalho](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. Selecione o nome da fonte de dados de entrada a partir do dropdown para ver as métricas de entrada. A fonte de entrada na imagem abaixo é chamada *de citações*. Para obter mais informações sobre as métricas de entrada, consulte [a monitorização do trabalho do Stream Analytics e como monitorizar as consultas.](stream-analytics-monitoring.md)

   > [!div class="mx-imgBorder"]
   > ![Métricas de entrada de diagrama de trabalho](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. Selecione um passo de consulta a partir do diagrama de trabalho ou selecione o nome do passo a partir do dropdown para ver as métricas do nível do passo. O atraso da marca de água é a única métrica disponível.

   > [!div class="mx-imgBorder"]
   > ![Métricas de passo](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. Selecione uma saída no diagrama ou a partir do dropdown para ver métricas relacionadas com a saída. Para obter mais informações sobre as métricas de saída, consulte [a monitorização do trabalho do Stream Analytics e como monitorizar as consultas.](stream-analytics-monitoring.md) Os lavatórios de produção ao vivo não são suportados.

   > [!div class="mx-imgBorder"]
   > ![Métricas de saída](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>Ver registos de diagnóstico

Os registos de diagnóstico de nível de trabalho contêm informações de diagnóstico para fontes de dados de entrada e pias de saída. Quando seleciona um nó de entrada ou nó de saída, apenas são apresentados os registos correspondentes. Não são apresentados registos se selecionar um passo de consulta. Pode encontrar todos os registos ao nível do trabalho e filtrar os registos por gravidade e tempo.

   > [!div class="mx-imgBorder"]
   > ![Registos de diagnóstico](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   Selecione uma entrada de registo para ver toda a mensagem.

   > [!div class="mx-imgBorder"]
   > ![Mensagem de registos de diagnóstico](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>Outras características do diagrama de trabalho

Pode selecionar **Stop** ou **Pause** da barra de ferramentas conforme necessário. Uma vez que o trabalho é interrompido, você pode retomá-lo a partir da última saída.

> [!div class="mx-imgBorder"]
> ![Parar ou fazer uma pausa](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

Selecione **Job Summary** no topo-direito do diagrama de trabalho para ver propriedades e configurações para o seu trabalho local.

> [!div class="mx-imgBorder"]
> ![Resumo do trabalho local](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>Limitações

* Pias de produção ao vivo não são suportadas em corridas locais.

* Executar trabalho localmente com função JavaScript só é suportado no sistema operativo Windows.

* O código personalizado C# e as funções de Aprendizagem automática Azure não são suportadas. 

* Apenas as opções de entrada em nuvem têm suporte [para políticas de tempo,](./stream-analytics-time-handling.md) enquanto as opções de entrada local não.

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Criar um trabalho stream analytics usando código de estúdio visual](quick-create-visual-studio-code.md)
* [Explore a Azure Stream Analytics com Código de Estúdio Visual](visual-studio-code-explore-jobs.md)
* [Teste Stream Analytics consultas localmente com dados de amostra usando Código de Estúdio Visual](visual-studio-code-local-run.md)
* [Teste Azure Stream Analytics empregos localmente com entrada ao vivo usando Código de Estúdio Visual](visual-studio-code-local-run-live-input.md)