---
title: Exportação utilizando Stream Analytics da Azure Application Insights [ Microsoft Docs
description: O Stream Analytics pode transformar, filtrar e encaminhar continuamente os dados que exporta a partir de Insights de Aplicação.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 15d1efa3a632024429d41f27fc23c569cd85bec2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536884"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Utilizar o Stream Analytics para processar dados exportados a partir de Insights de Aplicação
[O Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) é a ferramenta ideal para o processamento de dados [exportados de Insights de Aplicação.](export-telemetry.md) O Stream Analytics pode retirar dados de várias fontes. Pode transformar e filtrar os dados, e depois encaminhá-lo para uma variedade de pias.

Neste exemplo, vamos criar um adaptador que retira dados de Insights de Aplicação, renomea e processa alguns dos campos, e canaliza-os para o Power BI.

> [!WARNING]
> Existem formas muito melhores e mais [fáceis de apresentar dados](../../azure-monitor/app/export-power-bi.md )de Insights de Aplicação no Power BI . O caminho aqui ilustrado é apenas um exemplo para ilustrar como processar dados exportados.
> 
> 

![Diagrama de bloco para exportação através de SA para PBI](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Criar armazenamento em Azure
A exportação contínua produz sempre dados para uma conta de Armazenamento Azure, pelo que é necessário criar primeiro o armazenamento.

1. Crie uma conta de armazenamento "clássica" na sua subscrição no [portal Azure.](https://portal.azure.com)
   
   ![No portal Azure, escolha Novo, Dados, Armazenamento](./media/export-stream-analytics/030.png)
2. Criar um contentor
   
    ![No novo armazenamento, selecione Recipientes, clique no azulejo dos recipientes e, em seguida, adicione](./media/export-stream-analytics/040.png)
3. Copiar a chave de acesso ao armazenamento
   
    Em breve, precisará que seja configurado a entrada para o serviço de análise de fluxos.
   
    ![No armazenamento, abra Definições, Teclas e pegue uma cópia da Chave de Acesso Primário](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Inicie a exportação contínua para o armazenamento do Azure
[A exportação contínua](export-telemetry.md) transfere dados de Insights de Aplicação para o armazenamento do Azure.

1. No portal Azure, navegue para o recurso Application Insights que criou para a sua aplicação.
   
    ![Escolha Navegar, Informações de Aplicação, a sua aplicação](./media/export-stream-analytics/050.png)
2. Criar uma exportação contínua.
   
    ![Escolha configurações, exportação contínua, adicionar](./media/export-stream-analytics/060.png)

    Selecione a conta de armazenamento que criou anteriormente:

    ![Definir o destino de exportação](./media/export-stream-analytics/070.png)

    Detete os tipos de eventos que pretende ver:

    ![Escolha tipos de eventos](./media/export-stream-analytics/080.png)

1. Deixe acumular alguns dados. Senta-te e deixa que as pessoas usem a tua candidatura por uns tempos. A telemetria chegará e verá gráficos estatísticos em [exploradores métricos](../../azure-monitor/platform/metrics-charts.md) e eventos individuais em [pesquisa de diagnóstico.](../../azure-monitor/app/diagnostic-search.md) 
   
    E também, os dados serão exportados para o seu armazenamento. 
2. Inspecione os dados exportados. No Estúdio Visual, escolha **View / Cloud Explorer,** e abra O Azure/Armazenamento. (Se não tiver esta opção de menu, precisa de instalar o Azure SDK: Abra o diálogo do Novo Projeto e abra o Visual C# / Cloud / Obtenha o Microsoft Azure SDK para .NET.)
   
    ![](./media/export-stream-analytics/04-data.png)
   
    Tome nota da parte comum do nome do caminho, que é derivado do nome da aplicação e da chave de instrumentação. 

Os eventos são escritos para ficheiros blob em formato JSON. Cada ficheiro pode conter um ou mais eventos. Gostaríamos de ler os dados do evento e filtrar os campos que queremos. Há todo o tipo de coisas que podemos fazer com os dados, mas o nosso plano de hoje é usar o Stream Analytics para canalizar os dados para o Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Criar uma instância azure stream analytics
A partir do [portal Azure,](https://portal.azure.com/)selecione o serviço Azure Stream Analytics e crie um novo trabalho stream analytics:

![](./media/export-stream-analytics/SA001.png)

![](./media/export-stream-analytics/SA002.png)

Quando o novo trabalho for criado, selecione **Ir para o recurso**.

![](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Adicione uma nova entrada

![](./media/export-stream-analytics/SA004.png)

Desloque-o para receber a entrada da sua bolha de Exportação Contínua:

![](./media/export-stream-analytics/SA0005.png)

Agora vai precisar da chave de acesso primário da sua conta de armazenamento, que observou anteriormente. Delineie isto como a chave da conta de armazenamento.

### <a name="set-path-prefix-pattern"></a>Definir padrão de prefixo de caminho

**Certifique-se de definir o formato de data para YYYY-MM-DD (com traços).**

O Padrão de Prefixo path especifica onde o Stream Analytics encontra os ficheiros de entrada no armazenamento. É necessário defini-lo para corresponder à forma como a Exportação Contínua armazena os dados. Coloque assim:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Neste exemplo:

* `webapplication27`é o nome do recurso Application **Insights, todos os casos inferiores.**
* `1234...`é a chave de instrumentação do recurso Application Insights, **omitindo traços.** 
* `PageViews`é o tipo de dados que pretende analisar. Os tipos disponíveis dependem do filtro definido na Exportação Contínua. Examinar os dados exportados para ver os outros tipos disponíveis e ver o modelo de dados de [exportação.](export-data-model.md)
* `/{date}/{time}`é um padrão escrito literalmente.

> [!NOTE]
> Inspecione o armazenamento para se certificar de que acerta o caminho.
> 

## <a name="add-new-output"></a>Adicionar nova saída
Agora selecione o seu trabalho > **Outputs** > **Adicionar**.

![](./media/export-stream-analytics/SA006.png)


![Selecione o novo canal, clique em Saídas, Adicionar, Power BI](./media/export-stream-analytics/SA010.png)

Forneça o seu **trabalho ou conta escolar** para autorizar o Stream Analytics a aceder ao seu recurso Power BI. Em seguida, invente um nome para a saída e para o conjunto de dados e tabela power BI alvo.

## <a name="set-the-query"></a>Definir a consulta
A consulta rege a tradução da entrada para a saída.

Utilize a função Teste para verificar se obtém a saída certa. Dê-lhe os dados da amostra que tirou da página de inputs. 

### <a name="query-to-display-counts-of-events"></a>Consulta para exibir contagens de eventos
Colhe esta consulta:

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

* exportação-entrada é o pseudónimo que demos ao fluxo de entrada
* pbi-output é o pseudónimo de saída que definimos
* Utilizamos [Outer APPLY GetElements](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) porque o nome do evento está numa matriz JSON aninhada. Em seguida, o Select escolhe o nome do evento, juntamente com uma contagem do número de casos com esse nome no período de tempo. O [Grupo Por](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) cláusula agrupa os elementos em períodos de tempo de um minuto.

### <a name="query-to-display-metric-values"></a>Consulta para exibir valores métricos
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

* Esta consulta perfura a telemetria das métricas para obter o tempo do evento e o valor métrico. Os valores métricos estão dentro de uma matriz, por isso usamos o padrão OUTER APPLY GetElements para extrair as linhas. "myMetric" é o nome da métrica neste caso. 

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

* Esta consulta inclui valores das propriedades da dimensão sem depender de uma dimensão particular estar num índice fixo na matriz de dimensão.

## <a name="run-the-job"></a>Executar a tarefa
Pode selecionar uma data no passado para iniciar o trabalho. 

![Selecione o trabalho e clique em Consultar. Cola a amostra abaixo.](./media/export-stream-analytics/SA008.png)

Espera até o trabalho estar a funcionar.

## <a name="see-results-in-power-bi"></a>Ver resultados em Power BI
> [!WARNING]
> Existem formas muito melhores e mais [fáceis de apresentar dados](../../azure-monitor/app/export-power-bi.md )de Insights de Aplicação no Power BI . O caminho aqui ilustrado é apenas um exemplo para ilustrar como processar dados exportados.
> 
> 

Open Power BI com o seu trabalho ou conta escolar, e selecione o conjunto de dados e a tabela que definiu como a saída do trabalho stream analytics.

![No Power BI, selecione o seu conjunto de dados e campos.](./media/export-stream-analytics/200.png)

Agora pode utilizar este conjunto de dados em relatórios e dashboards no [Power BI](https://powerbi.microsoft.com).

![No Power BI, selecione o seu conjunto de dados e campos.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Não existem dados?
* Verifique se [definiu o formato da data](#set-path-prefix-pattern) corretamente para YYYY-MM-DD (com traços).

## <a name="video"></a>Vídeo
Noam Ben Zeev mostra como processar dados exportados usando o Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Exportação contínua](export-telemetry.md)
* [Referência detalhada do modelo de dados para os tipos e valores de propriedade.](export-data-model.md)
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)

