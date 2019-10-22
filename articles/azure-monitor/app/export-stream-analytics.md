---
title: Exportar usando Stream Analytics do Aplicativo Azure insights | Microsoft Docs
description: Stream Analytics pode transformar, filtrar e rotear continuamente os dados exportados de Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/08/2019
ms.openlocfilehash: 3be1a643cbe942c0b740ae8ebcc2c7f2dda24854
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677937"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Usar Stream Analytics para processar dados exportados de Application Insights
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) é a ferramenta ideal para o processamento [de dados exportados do Application insights](export-telemetry.md). Stream Analytics pode extrair dados de uma variedade de fontes. Ele pode transformar e filtrar os dados e, em seguida, roteá-los para uma variedade de coletores.

Neste exemplo, criaremos um adaptador que usa dados de Application Insights, renomeia e processa alguns dos campos e os canaliza em Power BI.

> [!WARNING]
> Há [maneiras recomendadas e mais fáceis de exibir Application insights dados no Power bi](../../azure-monitor/app/export-power-bi.md ). O caminho ilustrado aqui é apenas um exemplo para ilustrar como processar dados exportados.
> 
> 

![Diagrama de bloco para exportar por meio de SA para PBI](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Criar armazenamento no Azure
A exportação contínua sempre gera dados para uma conta de armazenamento do Azure, portanto, você precisa criar o armazenamento primeiro.

1. Crie uma conta de armazenamento "clássica" em sua assinatura no [portal do Azure](https://portal.azure.com).
   
   ![Em portal do Azure, escolha novo, dados, armazenamento](./media/export-stream-analytics/030.png)
2. Criar um contentor
   
    ![No novo armazenamento, selecione contêineres, clique no bloco contêineres e, em seguida, adicione](./media/export-stream-analytics/040.png)
3. Copiar a chave de acesso de armazenamento
   
    Você precisará dela em breve para configurar a entrada para o serviço do Stream Analytics.
   
    ![No armazenamento, abra configurações, chaves e faça uma cópia da chave de acesso primária](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Iniciar exportação contínua para o armazenamento do Azure
A [exportação contínua](export-telemetry.md) move dados de Application insights para o armazenamento do Azure.

1. Na portal do Azure, navegue até o recurso de Application Insights que você criou para seu aplicativo.
   
    ![Escolha procurar, Application Insights, seu aplicativo](./media/export-stream-analytics/050.png)
2. Crie uma exportação contínua.
   
    ![Escolha configurações, exportação contínua, adicionar](./media/export-stream-analytics/060.png)

    Selecione a conta de armazenamento que você criou anteriormente:

    ![Definir o destino de exportação](./media/export-stream-analytics/070.png)

    Defina os tipos de eventos que você deseja ver:

    ![Escolher tipos de evento](./media/export-stream-analytics/080.png)

1. Permitir que alguns dados sejam acumulados. Retorne e deixe que as pessoas usem seu aplicativo por algum tempo. A telemetria entrará e você verá gráficos estatísticos no [Gerenciador de métricas](../../azure-monitor/app/metrics-explorer.md) e eventos individuais na [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md). 
   
    Além disso, os dados serão exportados para o armazenamento. 
2. Inspecione os dados exportados. No Visual Studio, escolha **Exibir/Cloud Explorer**e abra Azure/armazenamento. (Se você não tiver essa opção de menu, precisará instalar o SDK do Azure: Abra a caixa de diálogo novo projeto C# e abra Visual/Cloud/obter Microsoft Azure SDK para .net.)
   
    ![](./media/export-stream-analytics/04-data.png)
   
    Anote a parte comum do nome do caminho, que é derivada do nome do aplicativo e da chave de instrumentação. 

Os eventos são gravados em arquivos de blob no formato JSON. Cada arquivo pode conter um ou mais eventos. Portanto, gostaríamos de ler os dados do evento e filtrar os campos que desejamos. Há todos os tipos de coisas que poderíamos fazer com os dados, mas nosso plano hoje é usar Stream Analytics para canalizar os dados para Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Criar uma instância de Azure Stream Analytics
No [portal do Azure](https://portal.azure.com/), selecione o serviço Azure Stream Analytics e crie um novo trabalho de Stream Analytics:

![](./media/export-stream-analytics/SA001.png)

![](./media/export-stream-analytics/SA002.png)

Quando o novo trabalho for criado, selecione **ir para o recurso**.

![](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Adicionar uma nova entrada

![](./media/export-stream-analytics/SA004.png)

Defina-a para obter entrada do seu blob de exportação contínua:

![](./media/export-stream-analytics/SA0005.png)

Agora você precisará da chave de acesso primária da sua conta de armazenamento, que você anotou anteriormente. Defina como a chave da conta de armazenamento.

### <a name="set-path-prefix-pattern"></a>Definir padrão de prefixo de caminho

**Certifique-se de definir o formato de data como aaaa-MM-DD (com traços).**

O padrão de prefixo de caminho especifica onde Stream Analytics localiza os arquivos de entrada no armazenamento. Você precisa defini-lo para corresponder à forma como a exportação contínua armazena os dados. Defina-o da seguinte forma:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Neste exemplo:

* `webapplication27` é o nome do recurso de Application Insights **todas as letras**minúsculas.
* `1234...` é a chave de instrumentação do recurso Application Insights, **omitindo traços**. 
* `PageViews` é o tipo de dados que você deseja analisar. Os tipos disponíveis dependem do filtro que você definiu na exportação contínua. Examine os dados exportados para ver os outros tipos disponíveis e veja o [modelo de dados de exportação](export-data-model.md).
* `/{date}/{time}` é um padrão escrito literalmente.

> [!NOTE]
> Inspecione o armazenamento para certificar-se de que você obtém o caminho certo.
> 

## <a name="add-new-output"></a>Adicionar nova saída
Agora, selecione seu trabalho > **saídas**  > **Adicionar**.

![](./media/export-stream-analytics/SA006.png)


![Selecione o novo canal, clique em saídas, adicionar Power BI](./media/export-stream-analytics/SA010.png)

Forneça sua **conta corporativa ou de estudante** para autorizar Stream Analytics acessar o recurso de Power bi. Em seguida, crie um nome para a saída e para o conjunto de Power BI e a tabela de destino.

## <a name="set-the-query"></a>Definir a consulta
A consulta governa a conversão de entrada para saída.

Use a função de teste para verificar se você obtém a saída correta. Dê a ele os dados de exemplo que você tirou na página de entradas. 

### <a name="query-to-display-counts-of-events"></a>Consulta para exibir contagens de eventos
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

* Export-Input é o alias que fornecemos à entrada do fluxo
* PBI-output é o alias de saída que definimos
* Usamos [outer aplica GetElements](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) porque o nome do evento está em uma matriz JSON aninhada. Em seguida, a seleção escolhe o nome do evento, junto com uma contagem do número de instâncias com esse nome no período de tempo. A cláusula [Group by](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) agrupa os elementos em períodos de tempo de um minuto.

### <a name="query-to-display-metric-values"></a>Consulta para exibir valores de métrica
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

* Essa consulta analisa a telemetria de métricas para obter a hora do evento e o valor da métrica. Os valores de métrica estão dentro de uma matriz, portanto, usamos o padrão de aplicação de GetElements para extrair as linhas. "mymetric" é o nome da métrica, nesse caso. 

### <a name="query-to-include-values-of-dimension-properties"></a>Consulta para incluir valores de propriedades de dimensão
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

* Essa consulta inclui valores das propriedades de dimensão sem depender de uma determinada dimensão estar em um índice fixo na matriz de dimensão.

## <a name="run-the-job"></a>Executar a tarefa
Você pode selecionar uma data no passado para iniciar o trabalho do. 

![Selecione o trabalho e clique em consulta. Cole o exemplo abaixo.](./media/export-stream-analytics/SA008.png)

Aguarde até que o trabalho esteja em execução.

## <a name="see-results-in-power-bi"></a>Veja os resultados em Power BI
> [!WARNING]
> Há [maneiras recomendadas e mais fáceis de exibir Application insights dados no Power bi](../../azure-monitor/app/export-power-bi.md ). O caminho ilustrado aqui é apenas um exemplo para ilustrar como processar dados exportados.
> 
> 

Abra Power BI com sua conta corporativa ou de estudante e selecione o conjunto de tabelas e a tabela que você definiu como a saída do trabalho de Stream Analytics.

![Em Power BI, selecione o conjunto de e os campos.](./media/export-stream-analytics/200.png)

Agora, você pode usar esse conjunto de os relatórios e painéis no [Power bi](https://powerbi.microsoft.com).

![Em Power BI, selecione o conjunto de e os campos.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Não tem dados?
* Verifique se você [definiu o formato de data](#set-path-prefix-pattern) corretamente para aaaa-mm-dd (com traços).

## <a name="video"></a>Vídeo
Noam Ben Zeev mostra como processar dados exportados usando Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Exportação contínua](export-telemetry.md)
* [Referência de modelo de dados detalhado para os tipos de propriedade e valores.](export-data-model.md)
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)

