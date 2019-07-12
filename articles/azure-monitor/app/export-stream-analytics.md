---
title: Exportar com o Stream Analytics do Azure Application Insights | Documentos da Microsoft
description: Stream Analytics continuamente pode transformar, filtrar e encaminhar os dados que exportou do Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: mbullwin
ms.openlocfilehash: d4a4196aa601fc8da79da3962faec026eff5ec87
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625067"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Utilizar o Stream Analytics para processar os dados exportados do Application Insights
[O Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) é a ferramenta ideal para processamento de dados [exportados do Application Insights](export-telemetry.md). Stream Analytics pode extrair dados de uma variedade de origens. Pode transformar e filtrar os dados e, em seguida, encaminhar para uma variedade de sinks.

Neste exemplo, vamos criar um adaptador que utiliza dados do Application Insights, muda e processa alguns dos campos e encaminha-o para o Power BI.

> [!WARNING]
> Há muito melhor e mais fácil [recomendado maneiras de exibir dados do Application Insights no Power BI](../../azure-monitor/app/export-power-bi.md ). O caminho ilustrado aqui é apenas um exemplo para ilustrar como processar os dados exportados.
> 
> 

![Diagrama de bloco para exportação por meio de SA para PBI](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Criar armazenamento no Azure
A exportação contínua sempre produz dados para uma conta de armazenamento do Azure, por isso terá de criar primeiro o armazenamento.

1. Criar uma conta de armazenamento "clássico" na sua subscrição no [portal do Azure](https://portal.azure.com).
   
   ![No portal do Azure, selecione o novo, dados, armazenamento](./media/export-stream-analytics/030.png)
2. Criar um contentor
   
    ![No novo armazenamento, selecionar contentores, clique no mosaico de contentores e, em seguida, adicionar](./media/export-stream-analytics/040.png)
3. Copie a chave de acesso de armazenamento
   
    Precisará dela em breve para configurar a entrada para o serviço do stream analytics.
   
    ![No armazenamento, abra as definições, chaves e fazer uma cópia da chave de acesso primária](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Iniciar a exportação contínua ao armazenamento do Azure
[A exportação contínua](export-telemetry.md) move os dados do Application Insights para o armazenamento do Azure.

1. No portal do Azure, navegue para o recurso do Application Insights que criou para a sua aplicação.
   
    ![Selecione procurar, Application Insights, seu aplicativo](./media/export-stream-analytics/050.png)
2. Crie uma exportação contínua.
   
    ![Escolha as definições, a exportação contínua, adicionar](./media/export-stream-analytics/060.png)

    Selecione a conta de armazenamento que criou anteriormente:

    ![Definir o destino de exportação](./media/export-stream-analytics/070.png)

    Defina os tipos de eventos que pretende ver:

    ![Escolha os tipos de eventos](./media/export-stream-analytics/080.png)

1. Permitir que alguns dados a acumularem. Relaxe e permitir que as pessoas utilizam a sua aplicação durante algum tempo. Telemetria chegarão e verá a gráficos de estatísticos [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md) e eventos individuais no [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md). 
   
    E, além disso, os dados vão exportar para o armazenamento. 
2. Inspecione os dados exportados. No Visual Studio, escolha **ver / na Cloud Explorer**e abra o Azure / armazenamento. (Se não tiver esta opção de menu, tem de instalar o SDK do Azure: Abra a caixa de diálogo novo projeto e abra o elemento Visual C# / na Cloud / obter o Microsoft Azure SDK para .NET.)
   
    ![](./media/export-stream-analytics/04-data.png)
   
    Tome nota da parte do nome do caminho, o que é derivado da chave de instrumentação e o nome de aplicação comuns. 

Os eventos são escritos para o blob de arquivos no formato JSON. Cada ficheiro pode conter um ou mais eventos. Portanto, gostaríamos de ler os dados de eventos e filtrar os campos que queremos. Existem todos os tipos de coisas que podemos fazer com os dados, mas o nosso plano é hoje a utilizar o Stream Analytics para encaminhar os dados para o Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Criar uma instância do Azure Stream Analytics
Partir do [portal do Azure](https://portal.azure.com/), selecione o serviço Azure Stream Analytics e criar uma nova tarefa de Stream Analytics:

![](./media/export-stream-analytics/SA001.png)

![](./media/export-stream-analytics/SA002.png)

Quando a nova tarefa é criada, selecione **Ir para recurso**.

![](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Adicionar uma nova entrada

![](./media/export-stream-analytics/SA004.png)

Defina-o para tomar a entrada do seu blob de exportação contínua:

![](./media/export-stream-analytics/SA0005.png)

Agora terá a chave de acesso primária da conta de armazenamento, o que anotou anteriormente. Defina esta opção como a chave de conta de armazenamento.

### <a name="set-path-prefix-pattern"></a>Padrão de prefixo do caminho de conjunto

**Não se esqueça de definir o formato de data como aaaa-MM-DD (com travessões).**

O padrão do prefixo do caminho Especifica onde o Stream Analytics localiza os ficheiros de entrada no armazenamento. Tem de defini-lo para corresponder à forma como a exportação contínua armazena os dados. Defini-lo assim:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Neste exemplo:

* `webapplication27` é o nome do recurso do Application Insights **todas as letras minúsculas**.
* `1234...` é a chave de instrumentação do recurso Application Insights, **omitindo travessões**. 
* `PageViews` é o tipo de dados que pretende analisar. Os tipos disponíveis dependem do filtro definido na exportação contínua. Examinar os dados exportados para ver os outros tipos disponíveis e veja a [exportar o modelo de dados](export-data-model.md).
* `/{date}/{time}` um padrão é escrito literalmente.

> [!NOTE]
> Inspecione o armazenamento para se certificar de que obtém o caminho certo.
> 

## <a name="add-new-output"></a>Adicionar nova saída
Agora, selecione a tarefa > **saídas** > **Add**.

![](./media/export-stream-analytics/SA006.png)


![Selecione o canal novo, clique em saídas, adicionar, o Power BI](./media/export-stream-analytics/SA010.png)

Fornecer seu **conta escolar ou profissional** para autorizar o Stream Analytics para aceder ao seu recurso do Power BI. Em seguida, criar um nome para a saída e para o conjunto de dados do destino Power BI e a tabela.

## <a name="set-the-query"></a>Definir a consulta
A consulta rege a conversão de entrada para a saída.

Utilize a função de teste para verificar o que obtém o resultado correto. Dê a ele os dados de exemplo que tirou da página de entradas. 

### <a name="query-to-display-counts-of-events"></a>Consulta para exibir as contagens de eventos
Cole esta consulta:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* introdução de exportação é o alias que demos no fluxo de entrada
* saída de pbi é o alias de saída que definimos
* Usamos [externa GetElements aplicar](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) porque o nome do evento está numa matriz JSON aninhada. Em seguida, selecione escolhe o nome do evento, juntamente com uma contagem do número de instâncias com esse nome no período de tempo. O [Group By](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) cláusula agrupa os elementos em períodos de tempo de um minuto.

### <a name="query-to-display-metric-values"></a>Consulta para exibir os valores de métrica
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Esta consulta detalha a telemetria de métricas para obter a hora do evento e o valor de métrica. Os valores de métrica estão dentro de uma matriz, pelo que vamos utilizar o padrão de externa GetElements aplicam-se para extrair as linhas. "myMetric" é o nome da métrica neste caso. 

### <a name="query-to-include-values-of-dimension-properties"></a>Consulta para incluir os valores das propriedades de dimensão
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Esta consulta inclui valores das propriedades de dimensão sem consoante a dimensão específica que está a ser de um índice fixo da matriz de dimensão.

## <a name="run-the-job"></a>Executar a tarefa
Pode selecionar uma data no passado para iniciar a tarefa de. 

![Selecione a tarefa e clique em consulta. Cole o exemplo abaixo.](./media/export-stream-analytics/SA008.png)

Aguarde até que a tarefa está em execução.

## <a name="see-results-in-power-bi"></a>Ver resultados no Power BI
> [!WARNING]
> Há muito melhor e mais fácil [recomendado maneiras de exibir dados do Application Insights no Power BI](../../azure-monitor/app/export-power-bi.md ). O caminho ilustrado aqui é apenas um exemplo para ilustrar como processar os dados exportados.
> 
> 

Abrir o Power BI no seu trabalho ou escolar e selecione o conjunto de dados e a tabela que definiu como o resultado da tarefa do Stream Analytics.

![No Power BI, selecione o seu conjunto de dados e campos.](./media/export-stream-analytics/200.png)

Agora, pode utilizar este conjunto de dados nos relatórios e dashboards nas [Power BI](https://powerbi.microsoft.com).

![No Power BI, selecione o seu conjunto de dados e campos.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Não existem dados?
* Verifique que [defina o formato de data](#set-path-prefix-pattern) corretamente para DD-MM-AAAA (com travessões).

## <a name="video"></a>Vídeo
Noam Ben Zeev mostra como processar os dados exportados com o Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Passos Seguintes
* [Exportação contínua](export-telemetry.md)
* [Referência para os tipos de propriedade e os valores do modelo de dados detalhados.](export-data-model.md)
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)

