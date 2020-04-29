---
title: Debug Azure Stream Analytics consulta localmente usando diagrama de trabalho em Estúdio Visual
description: Este artigo descreve como depurar consultas localmente usando diagrama de trabalho em Ferramentas de Análise de Fluxo de Azure para Estúdio Visual.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 106b1f0b765700803d2cd55b5e049fae5be3dfad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76847202"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Debug Azure Stream Analytics consulta localmente usando diagrama de trabalho em Estúdio Visual

Empregos que não produzam resultados ou resultados inesperados são cenários comuns de resolução de problemas para consultas de streaming. Pode utilizar o diagrama de trabalho enquanto testa a sua consulta localmente no Estúdio Visual para examinar o conjunto de resultados intermédios e métricas para cada passo. Os diagramas de trabalho podem ajudá-lo a isolar rapidamente a origem de um problema quando se desatiram problemas.

## <a name="debug-a-query-using-job-diagram"></a>Depurar uma consulta usando diagrama de trabalho

Um script Azure Stream Analytics é usado para transformar dados de entrada em dados de saída. O diagrama de trabalho mostra como os dados fluem de fontes de entrada (Event Hub, IoT Hub, etc.) através de múltiplos passos de consulta e, finalmente, para afundações de saída. Cada passo de consulta é mapeado para um `WITH` conjunto de resultados temporário definido no script usando uma declaração. Pode ver os dados, bem como as métricas de cada passo de consulta em cada resultado intermédio definido para encontrar a origem de um problema.

> [!NOTE]
> Este diagrama de trabalho apenas mostra os dados e métricas para testes locais num único nó. Não deve ser utilizado para afinação de desempenho e resolução de problemas.

### <a name="start-local-testing"></a>Iniciar testes locais

Use este [Quickstart](stream-analytics-quick-create-vs.md) para aprender a criar um trabalho de Stream Analytics usando o Visual Studio ou [exportar um trabalho existente para um projeto local.](stream-analytics-vs-tools.md#export-jobs-to-a-project) Se quiser testar a consulta com os dados de entrada locais, siga estas [instruções](stream-analytics-live-data-local-testing.md). Se quiser testar com a entrada ao vivo, passe para o próximo passo.

> [!NOTE]
> Se exportar um emprego para o projeto local e quiser testar contra um fluxo de entrada ao vivo, precisa especificar as credenciais para todas as entradas novamente.  

Escolha a fonte de entrada e saída do editor de scripts e selecione **Executar localmente**. O diagrama de trabalho aparece no lado direito.

### <a name="view-the-intermediate-result-set"></a>Ver o conjunto de resultados intermédios  

1. Selecione o passo de consulta para navegar para o script. É automaticamente direcionado para o script correspondente no editor à esquerda.

   ![Diagrama de trabalho navegar script](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Selecione o passo de consulta e selecione **Preview** no diálogo apareceu. O conjunto de resultados é mostrado num separador na janela inferior do resultado.

   ![Resultado da pré-visualização do diagrama de trabalho](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Ver métricas de passo

Nesta secção, você explora as métricas disponíveis para cada parte do diagrama.

#### <a name="input-sources-live-stream"></a>Fontes de entrada (live stream)

![Diagrama de trabalho fontes de entrada ao vivo](./media/debug-locally-using-job-diagram/live-input.png)

|Métrica|Descrição|
|-|-|
|**TaxiRide**| O nome da entrada.|
|**Hub de Eventos** | Tipo de fonte de entrada.|
|**Eventos**|O número de eventos lidos.|
|**Fontes de eventos atrasados**|Quantas mais mensagens precisam de ser lidas para os Centros de Eventos e inputs do IoT Hub.|
|**Eventos em Bytes**|O número de bytes lidos.|
| **Eventos Degradados**|A contagem de eventos que teve um problema que não seja a desserialização.|
|**Eventos Iniciais**| O número de eventos que têm um carimbo de tempo de aplicação antes da marca de água elevada.|
|**Eventos Tardios**| O número de eventos que têm um carimbo de tempo de aplicação após a marca de água elevada.|
|**Fontes de eventos**| O número de unidades de dados lidas. Por exemplo, o número de bolhas.|

#### <a name="input-sources-local-input"></a>Fontes de entrada (entrada local)

![Fontes de entrada locais do diagrama de trabalho](./media/debug-locally-using-job-diagram/local-input.png)

|Métrica|Descrição|
|-|-|
|**TaxiRide**| O nome da entrada.|
|**Contagem de fileiras**| O número de linhas geradas a partir do degrau.|
|**Tamanho dos dados**| O tamanho dos dados gerados a partir deste passo.|
|**Entrada local**| Use os dados locais como entrada.|

#### <a name="query-steps"></a>Passos da consulta

![Passo de consulta do diagrama de trabalho](./media/debug-locally-using-job-diagram/query-step.png)

|Métrica|Descrição|
|-|-|
|**TripData**|O nome do conjunto de resultados temporários.|
|**Contagem de fileiras**| O número de linhas geradas a partir do degrau.|
|**Tamanho dos dados**| O tamanho dos dados gerados a partir deste passo.|
  
#### <a name="output-sinks-live-output"></a>Sumidouros de saída (saída ao vivo)

![Diagrama de trabalho pias de saída local](./media/debug-locally-using-job-diagram/live-output.png)

|Métrica|Descrição|
|-|-|
|**regionalggEH**|O nome da saída.|
|**Eventos**|O número de eventos a chegar aos afundados.|

#### <a name="output-sinks-local-output"></a>Sumidouros de saída (saída local)

![Diagrama de trabalho pias de saída local](./media/debug-locally-using-job-diagram/local-output.png)

|Métrica|Descrição|
|-|-|
|**regionalggEH**|O nome da saída.|
|**Saída Local**| Resultado de saída para um arquivo local.|
|**Contagem de fileiras**| O número de filas de saída para o arquivo local.|
|**Tamanho dos dados**| O tamanho da saída de dados para o arquivo local.|

### <a name="close-job-diagram"></a>Diagrama de trabalho fechado

Se já não precisar do diagrama de trabalho, selecione **Fechar** no canto superior direito. Depois de fechar a janela do diagrama, precisa de começar os testes locais novamente para vê-lo.

### <a name="view-job-level-metrics-and-stop-running"></a>Ver métricas de nível de trabalho e parar de correr

Outras métricas de nível de trabalho aparecem na consola pop-up. Prima **Ctrl+C** na consola se quiser parar o trabalho.

![Trabalho de paragem do diagrama de trabalho](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Limitações

* Os lavatórios de saída do Power BI e do Azure Data Lake Gen1 não são suportados devido a limitações do modelo de autenticação.

* Apenas as opções de entrada em nuvem têm apoio de políticas de [tempo,](stream-analytics-out-of-order-and-late-events.md) enquanto as opções locais de entrada não.

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Criar um trabalho de Stream Analytics usando o Estúdio Visual](stream-analytics-quick-create-vs.md)
* [Use o Estúdio Visual para ver empregos da Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Teste dados ao vivo localmente utilizando ferramentas Azure Stream Analytics para Estúdio Visual (Pré-visualização)](stream-analytics-live-data-local-testing.md)
