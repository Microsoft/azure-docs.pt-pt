---
title: Criar um dashboard em tempo real usando Azure Cosmos DB, Azure Analysis Services e Power BI
description: Saiba como criar um dashboard meteorológico ao vivo no Power BI usando os Serviços de Análise Azure Cosmos DB e Azure.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/04/2019
ms.reviewer: sngun
ms.openlocfilehash: f44a8d82ea2588abad6855fd8eaf7aed34256d87
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370768"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Criar um dashboard em tempo real usando Azure Cosmos DB e Power BI

Este artigo descreve os passos necessários para criar um dashboard meteorológico vivo no Power BI usando os Serviços de Análise Azure Cosmos DB e Azure Analysis Services. O painel Power BI apresentará gráficos para mostrar informações em tempo real sobre a temperatura e a precipitação numa região.

## <a name="reporting-scenarios"></a>Cenários de reporte

Existem várias formas de configurar dashboards de reporte em dados armazenados em Azure Cosmos DB. Dependendo dos requisitos de estagnação e do tamanho dos dados, a tabela seguinte descreve a configuração de relatórios para cada cenário:


|Cenário |Configuração |
|---------|---------|
|1. Gerar relatórios ad-hoc (sem atualização)    |  [Conector DB power BI Azure Cosmos com modo de importação](powerbi-visualize.md)       |
|2. Gerar relatórios ad-hoc com atualização periódica   |  [Conector DB Power BI Azure Cosmos com modo de importação (Atualização periódica programada)](powerbi-visualize.md)       |
|3. Relatório sobre grandes conjuntos de dados (< 10 GB)     |  Conector DB power BI Azure Cosmos com atualização incremental       |
|4. Reportar tempo real em grandes conjuntos de dados    |  Conector de Serviços de Análise Power BI Azure com consulta direta + Serviços de Análise Azure (conector DB Azure Cosmos)       |
|5. Relatório sobre dados ao vivo com agregados     |  [Conector de faísca BI De potência com consulta direta + Azure Databricks + Conector Cosmos DB Spark.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Relatório sobre dados ao vivo com agregados em grandes conjuntos de dados   |  Conector de Serviços de Análise power BI Azure com consulta direta + Serviços de Análise Azure + Azure Databricks + Conector Cosmos DB Spark.       |

Os cenários 1 e 2 podem ser facilmente configurado usando o conector Azure Cosmos DB Power BI. Este artigo descreve as configurações para os cenários 3 e 4.

### <a name="power-bi-with-incremental-refresh"></a>Power BI com atualização incremental

O Power BI tem um modo onde a atualização incremental pode ser configurada. Este modo elimina a necessidade de criar e gerir as divisórias dos Serviços de Análise Azure. A atualização incremental pode ser configurada para filtrar apenas as últimas atualizações em grandes conjuntos de dados. No entanto, este modo funciona apenas com o serviço Power BI Premium que tem uma limitação de conjunto de dados de 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Conector de análise power BI Azure + Serviços de Análise Azure

A Azure Analysis Services fornece uma plataforma totalmente gerida como um serviço que acolhe modelos de dados de nível empresarial na nuvem. Conjuntos de dados maciços podem ser carregados da Azure Cosmos DB para os Serviços de Análise Azure. Para evitar a consulta de todo o conjunto de dados a toda a hora, os conjuntos de dados podem ser subdivididos em partições dos Serviços de Análise Azure, que podem ser atualizados de forma independente em diferentes frequências.

## <a name="power-bi-incremental-refresh"></a>Atualização incremental do Power BI

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingerir dados meteorológicos em Azure Cosmos DB

Crie um oleoduto de ingestão para carregar [os dados meteorológicos](https://catalog.data.gov/dataset/local-weather-archive/resource/c28974a2-fc83-4722-8977-9a701323f729) para a Azure Cosmos DB. Pode configurar um trabalho [da Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) para carregar periodicamente os dados meteorológicos mais recentes em Azure Cosmos DB utilizando a pia HTTP Source e Cosmos DB.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Ligue o Power BI ao Azure Cosmos DB

1. **Ligue a conta Azure Cosmos ao Power BI** - Abra o Power BI Desktop e utilize o conector DB Azure Cosmos para selecionar a base de dados e o recipiente certos.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png" alt-text="Conector do Power BI do Azure Cosmos DB":::

1. **Configure atualização incremental** - Siga os passos em [atualização incremental com](/power-bi/service-premium-incremental-refresh) o artigo Power BI para configurar atualização incremental para o conjunto de dados. Adicione os parâmetros **RangeStart** e **RangeEnd,** como mostrado na seguinte imagem:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png" alt-text="Conector do Power BI do Azure Cosmos DB" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   Dependendo da coluna e do tipo de dados presentes no conjunto de dados de origem, pode alterar os campos RangeStart e RangeEnd em conformidade

   
   |Propriedade  |Tipo de dados  |Filtro  |
   |---------|---------|---------|
   |_ts     |   Numérico      |  [_ts] > Duração.TotalSeconds (RangeStart - #datetime(1970, 1, 1, 0, 0, 0)) e [_ts] < Duração.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0,0))       |
   |Data (por exemplo:- 2019-08-19)     |   String      | [Document.date]> DateTime.ToText (RangeStart,"yyyy-MM-dd") e [document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd")        |
   |Data (por exemplo:- 2019-08-11 12:00:00)   |  String       |  [Document.date]> DateTime.ToText(RangeStart," yyyy-mm-dd HH:mm:ss") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-mm-dd HH:mm:ss")       |


1. **Defina a política de atualização** - Defina a política de atualização navegando para o **separador de atualização Incremental** no menu **de contexto** da tabela. Desacora a política de atualização para atualizar **todos os dias** e armazenar os dados do último mês.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png" alt-text="Conector do Power BI do Azure Cosmos DB":::

   Ignore o aviso que diz que *a consulta M não pode ser confirmada para ser dobrada*. O conector Azure Cosmos DB dobra consultas de filtro.

1. **Carregue os dados e gere os relatórios** - Utilizando os dados que carregou anteriormente, crie as tabelas para reportar sobre a temperatura e a precipitação.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Conector do Power BI do Azure Cosmos DB":::

1. **Publique o relatório ao Power BI premium** - Uma vez que a atualização incremental é apenas uma característica Premium, o diálogo de publicação só permite a seleção de um espaço de trabalho na capacidade Premium. A primeira atualização poderá demorar mais tempo a importar os dados do histórico. As atualizações de dados subsequentes são muito mais rápidas porque utilizam atualização incremental.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Conector de análise power BI Azure + Serviços de Análise Azure 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingerir dados meteorológicos em Azure Cosmos DB 

Crie um oleoduto de ingestão para carregar [os dados meteorológicos](https://catalog.data.gov/dataset/local-weather-archive/resource/c28974a2-fc83-4722-8977-9a701323f729) para a Azure Cosmos DB. Pode configurar um trabalho da Azure Data Factory (ADF) para carregar periodicamente os dados meteorológicos mais recentes em Azure Cosmos DB utilizando a pia HTTP Source e Cosmos DB.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Ligue os Serviços de Análise Azure à conta Azure Cosmos

1. **Criar um novo cluster**  -  de Serviços de Análise Azure [Crie um exemplo de serviços de Análise Azure](../analysis-services/analysis-services-create-server.md) na mesma região que a conta Azure Cosmos e o cluster Databricks.

1. **Criar um novo Projeto Tabular de Serviços de Análise em Estúdio**  -   Visual [Instale as Ferramentas de Dados do Servidor SQL (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017&preserve-view=true) e crie um projeto Tabular de Serviços de Análise em Estúdio Visual.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png" alt-text="Conector do Power BI do Azure Cosmos DB":::

   Escolha a instância integrada do **espaço de trabalho** e o conjunto do Nível de Compatibilidade para **SQL Server 2017 / Azure Analysis Services (1400)**

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png" alt-text="Conector do Power BI do Azure Cosmos DB":::

1. **Adicione a fonte de dados DB do Azure Cosmos** - Navegue para **Models** >  **modelos Fontes de Dados**  >  **Novas Fontes** de Dados e adicione a fonte de dados DB Azure Cosmos, como mostrado na seguinte imagem:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/add-data-source.png" alt-text="Conector do Power BI do Azure Cosmos DB":::

   Ligue-se à Azure Cosmos DB fornecendo a **conta URI,** **nome da base de dados,** e o nome do **recipiente**. Agora pode ver que os dados do contentor Azure Cosmos são importados para o Power BI.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png" alt-text="Conector do Power BI do Azure Cosmos DB":::

1. **Construa o modelo de Serviços de Análise** - Abra o editor de consulta, execute as operações necessárias para otimizar o conjunto de dados carregado:

   * Extrair apenas as colunas relacionadas com o tempo (temperatura e precipitação)

   * Extraia a informação do mês da mesa. Estes dados são úteis na criação de divisórias, conforme descrito na secção seguinte.

   * Converter as colunas de temperatura em número

   A expressão M resultante é a seguinte:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Além disso, altere o tipo de dados das colunas de temperatura para Decimal para se certificar de que estes valores podem ser traçados no Power BI.

1. **Criar partições de Análise Azure** - Criar divisórias nos Serviços de Análise Azure para dividir o conjunto de dados em divisórias lógicas que podem ser atualizadas de forma independente e em diferentes frequências. Neste exemplo, cria-se duas divisórias que dividiriam o conjunto de dados nos dados do mês mais recente e em tudo o resto.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png" alt-text="Conector do Power BI do Azure Cosmos DB" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Histórico** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Implementar o Modelo no Servidor de Análise Azure** - Clique no projeto Azure Analysis Services e escolha **Implementar**. Adicione o nome do servidor no painel **de propriedades do Servidor de Implementação.**

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png" alt-text="Conector do Power BI do Azure Cosmos DB":::

1. **Configure as atualizações e as fusões** de partição - Azure Analysis Services permite o processamento independente de divisórias. Uma vez que queremos que a partição do **Último Mês** seja constantemente atualizada com os dados mais recentes, desajei o intervalo de atualização para 5 minutos. Pode atualizar os dados utilizando a [API REST,](../analysis-services/analysis-services-async-refresh.md) [automação Azure,](../analysis-services/analysis-services-refresh-azure-automation.md)ou com uma [Aplicação Lógica.](../analysis-services/analysis-services-refresh-logic-app.md) Não é necessário refrescar os dados em partição histórica. Além disso, você precisa escrever algum código para consolidar a divisão do último mês para a partição histórica e criar uma nova partição do último mês.

## <a name="connect-power-bi-to-analysis-services"></a>Ligar o Power BI aos serviços de análise

1. **Ligue-se ao Servidor de Análise Azure utilizando o Conector de Base de Serviços de Análise Azure** - Escolha o **modo Live** e ligue-se à instância dos Serviços de Análise Azure, tal como mostrado na seguinte imagem:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png" alt-text="Conector do Power BI do Azure Cosmos DB":::

1. **Carregue os dados e gere relatórios** - Utilizando os dados que carregou anteriormente, crie gráficos para reportar sobre a temperatura e a precipitação. Uma vez que está a criar uma ligação ao vivo, as consultas devem ser executadas nos dados do modelo Azure Analysis Services que implementou no passo anterior. Os gráficos de temperatura serão atualizados dentro de cinco minutos após a colocação dos novos dados no Azure Cosmos DB.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Conector do Power BI do Azure Cosmos DB":::

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o Power BI, consulte [Começar com o Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Ligue o Qlik Sense ao Azure Cosmos DB e visualize os seus dados](visualize-qlik-sense.md)
