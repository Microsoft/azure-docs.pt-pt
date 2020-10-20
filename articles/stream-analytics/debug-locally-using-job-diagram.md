---
title: Debug Azure Stream Analytics consulta localmente usando diagrama de trabalho em Estúdio Visual
description: Este artigo descreve como depurar consultas localmente usando o diagrama de trabalho em Azure Stream Analytics Tools for Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: e9d6bbdf325c0da96b81c1f2146da284ea744def
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218260"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Debug Azure Stream Analytics consulta localmente usando diagrama de trabalho em Estúdio Visual

Empregos que não resultam nem resultados inesperados são cenários comuns de resolução de problemas para consultas de streaming. Pode utilizar o diagrama de trabalho enquanto testa a sua consulta localmente no Visual Studio para examinar o conjunto de resultados intermédios e as métricas para cada passo. Os diagramas de trabalho podem ajudá-lo a isolar rapidamente a origem de um problema quando resolver problemas.

## <a name="debug-a-query-using-job-diagram"></a>Depurar uma consulta usando o diagrama de trabalho

Um script Azure Stream Analytics é usado para transformar dados de entrada para dados de saída. O diagrama de trabalho mostra como os dados fluem de fontes de entrada (Event Hub, IoT Hub, etc.) através de múltiplos passos de consulta e, finalmente, para sumidouros de saída. Cada passo de consulta é mapeado para um conjunto de resultados temporário definido no script usando uma `WITH` declaração. Pode ver os dados, bem como as métricas de cada passo de consulta em cada conjunto de resultados intermédios para encontrar a origem de um problema.

> [!NOTE]
> Este diagrama de trabalho apenas mostra os dados e métricas para testes locais num único nó. Não deve ser utilizado para afinação de desempenho e resolução de problemas.

### <a name="start-local-testing"></a>Iniciar testes locais

Utilize este [Quickstart](stream-analytics-quick-create-vs.md) para aprender a criar um trabalho stream Analytics usando o Visual Studio ou [exporte um emprego existente para um projeto local.](stream-analytics-vs-tools.md#export-jobs-to-a-project) Se pretender testar a consulta com os dados de entrada locais, siga estas [instruções](stream-analytics-live-data-local-testing.md). Se quiser testar com entrada ao vivo, passe para o próximo passo.

> [!NOTE]
> Se exportar um emprego para um projeto local e quiser testar contra um fluxo de entrada ao vivo, precisa especificar as credenciais para todas as entradas novamente.  

Escolha a fonte de entrada e saída do editor de scripts e selecione **Executar localmente.** O diagrama de trabalho aparece no lado direito.

### <a name="view-the-intermediate-result-set"></a>Ver o conjunto de resultados intermédios  

1. Selecione o passo de consulta para navegar para o script. É automaticamente direcionado para o script correspondente no editor à esquerda.

   ![Diagrama de trabalho navegando script](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Selecione o passo de consulta e **selecione Preview** no diálogo poped up. O conjunto de resultados é mostrado numa lingueta na janela de resultados inferiores.

   ![Resultado da pré-visualização do diagrama de trabalho](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Ver métricas de passo

Nesta secção, você explora as métricas disponíveis para cada parte do diagrama.

#### <a name="input-sources-live-stream"></a>Fontes de entrada (Live stream)

![Fontes de entrada ao vivo do diagrama de trabalho](./media/debug-locally-using-job-diagram/live-input.png)

|Métrica|Descrição|
|-|-|
|**TaxiRide**| O nome da entrada.|
|**Hub de Eventos** | Tipo de fonte de entrada.|
|**Eventos**|O número de eventos lidos.|
|**Fontes de eventos retrostruidas**|Quantas mensagens mais precisam de ser lidas para os Centros de Eventos e entradas IoT Hub.|
|**Eventos em Bytes**|O número de bytes lidos.|
| **Eventos Degradados**|A contagem de eventos que tiveram outro problema que não a deserialização.|
|**Eventos Iniciais**| O número de eventos que têm um horário de inscrição antes da marca de água alta.|
|**Eventos Tardios**| O número de eventos que têm um carimbo de tempo de aplicação após a marca de água elevada.|
|**Origens de Eventos**| O número de unidades de dados lidas. Por exemplo, o número de bolhas.|

#### <a name="input-sources-local-input"></a>Fontes de entrada (entrada local)

![Fontes de entrada locais do diagrama de trabalho](./media/debug-locally-using-job-diagram/local-input.png)

|Métrica|Descrição|
|-|-|
|**TaxiRide**| O nome da entrada.|
|**Contagem de Linhas**| O número de linhas geradas a partir do degrau.|
|**Tamanho dos dados**| O tamanho dos dados gerados a partir deste passo.|
|**Entrada local**| Use os dados locais como entrada.|

#### <a name="query-steps"></a>Passos da consulta

![Passo de consulta do diagrama de trabalho](./media/debug-locally-using-job-diagram/query-step.png)

|Métrica|Descrição|
|-|-|
|**TripData**|O nome do conjunto de resultados temporários.|
|**Contagem de Linhas**| O número de linhas geradas a partir do degrau.|
|**Tamanho dos dados**| O tamanho dos dados gerados a partir deste passo.|
  
#### <a name="output-sinks-live-output"></a>Pias de saída (saída ao vivo)

![Diagrama de trabalho que mostra os lavatórios de saída locais.](./media/debug-locally-using-job-diagram/live-output.png)

|Métrica|Descrição|
|-|-|
|**regionaggEH**|O nome da saída.|
|**Eventos**|O número de eventos a afundar.|

#### <a name="output-sinks-local-output"></a>Pias de saída (saída local)

![Diagrama de trabalho pias de saída locais](./media/debug-locally-using-job-diagram/local-output.png)

|Métrica|Descrição|
|-|-|
|**regionaggEH**|O nome da saída.|
|**Saída local**| Resultado de saída para um ficheiro local.|
|**Contagem de Linhas**| O número de linhas de saída para o arquivo local.|
|**Tamanho dos dados**| O tamanho da saída de dados para o ficheiro local.|

### <a name="close-job-diagram"></a>Diagrama de trabalho próximo

Se já não precisar do diagrama de trabalho, **selecione Close** no canto superior direito. Depois de fechar a janela do diagrama, tem de recomeçar os testes locais para o ver.

### <a name="view-job-level-metrics-and-stop-running"></a>Ver métricas de nível de trabalho e parar de correr

Outras métricas de nível de trabalho aparecem na consola pop-up. Prima **Ctrl+C** na consola se quiser parar o trabalho.

![Diagrama de trabalho parar o trabalho](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Limitações

* Os lavatórios de produção da Power BI e do Azure Data Lake Storage Gen1 não são suportados devido a limitações do modelo de autenticação.

* Apenas as opções de entrada na nuvem têm suporte às políticas de [tempo,](stream-analytics-out-of-order-and-late-events.md) enquanto as opções de entrada local não.

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Criar um trabalho stream analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Use o Estúdio Visual para ver os trabalhos do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Teste dados ao vivo localmente usando ferramentas Azure Stream Analytics para Estúdio Visual (Pré-visualização)](stream-analytics-live-data-local-testing.md)
