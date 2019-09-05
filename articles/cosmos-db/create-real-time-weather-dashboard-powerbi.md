---
title: Criar um painel em tempo real usando Azure Cosmos DB, Azure Analysis Services e Power BI
description: Saiba como criar um painel do clima ao vivo em Power BI usando Azure Cosmos DB e Azure Analysis Services.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376596"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Criar um painel em tempo real usando Azure Cosmos DB e Power BI

Este artigo descreve as etapas necessárias para criar um painel do clima ao vivo no Power BI usando Azure Cosmos DB e Azure Analysis Services. O painel de Power BI exibirá gráficos para mostrar informações em tempo real sobre temperatura e chuva em uma região.

## <a name="reporting-scenarios"></a>Cenários de relatórios

Há várias maneiras de configurar painéis de relatório em dados armazenados no Azure Cosmos DB. Dependendo dos requisitos de desatualização e do tamanho dos dados, a tabela a seguir descreve a configuração de relatórios para cada cenário:


|Cenário |Configurar |
|---------|---------|
|1. Gerando relatórios ad hoc (sem atualização)    |  [Power BI conector de Azure Cosmos DB com o modo de importação](powerbi-visualize.md)       |
|2. Gerando relatórios ad hoc com atualização periódica   |  [Power BI conector de Azure Cosmos DB com o modo de importação (atualização periódica agendada)](powerbi-visualize.md)       |
|3. Relatórios sobre grandes conjuntos de dados (< 10 GB)     |  Conector de Azure Cosmos DB Power BI com atualização incremental       |
|4. Relatórios em tempo real em grandes conjuntos de dados    |  Conector de Azure Analysis Services Power BI com Direct Query + Azure Analysis Services (conector Azure Cosmos DB)       |
|5. Relatórios sobre dados dinâmicos com agregações     |  [Power BI conector do Spark com Direct Query + Azure Databricks + Cosmos DB o conector do Spark.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Relatórios sobre dados dinâmicos com agregações em conjuntos de dados grandes   |  Power BI Azure Analysis Services Connector com Direct Query + Azure Analysis Services + Azure Databricks + Cosmos DB conector do Spark.       |

Os cenários 1 e 2 podem ser facilmente configurados usando o conector de Power BI Azure Cosmos DB. Este artigo descreve as configurações para os cenários 3 e 4.

### <a name="power-bi-with-incremental-refresh"></a>Power BI com atualização incremental

Power BI tem um modo em que a atualização incremental pode ser configurada. Esse modo elimina a necessidade de criar e gerenciar partições de Azure Analysis Services. A atualização incremental pode ser configurada para filtrar somente as atualizações mais recentes em grandes conjuntos de altos. No entanto, esse modo funciona apenas com Power BI Premium serviço que tem uma limitação de conjunto de um DataSet de 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI o conector de análise do Azure + Azure Analysis Services

O Azure Analysis Services fornece uma plataforma totalmente gerenciada como um serviço que hospeda modelos de dados de nível empresarial na nuvem. Conjuntos de dados maciços podem ser carregados de Azure Cosmos DB para Azure Analysis Services. Para evitar a consulta de todo o conjunto de todos os tempos, os conjuntos de valores podem ser subdivididos em partições Azure Analysis Services, que podem ser atualizadas independentemente em frequências diferentes.

## <a name="power-bi-incremental-refresh"></a>Power BI atualização incremental

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingerir dados meteorológicos em Azure Cosmos DB

Configure um pipeline de ingestão para carregar [dados meteorológicos](https://catalog.data.gov/dataset/local-weather-archive) para Azure Cosmos DB. Você pode configurar um trabalho de [Azure data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) para carregar periodicamente os dados meteorológicos mais recentes em Azure Cosmos DB usando o coletor de Cosmos DB e de origem http.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Conectar Power BI ao Azure Cosmos DB

1. **Conectar a conta do Azure Cosmos ao Power bi** -abra o Power bi desktop e use o conector do Azure Cosmos DB para selecionar o banco de dados e o contêiner corretos.

   ![Conector do Power BI do Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Configurar a atualização incremental** -siga as etapas em [atualização incremental com Power bi](/power-bi/service-premium-incremental-refresh) artigo para configurar a atualização incremental do conjunto de um. Adicione os parâmetros **RangeStart** e **rangeEnd** , conforme mostrado na seguinte captura de tela:

   ![Configurar parâmetros de intervalo](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Como o conjunto de dados tem uma coluna de data que está na forma de texto, os parâmetros **RangeStart** e **rangeEnd** devem ser transformados para usar o filtro a seguir. No painel de **Editor avançado** , modifique a consulta adicione o seguinte texto para filtrar as linhas com base nos parâmetros rangeStart e rangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   Dependendo de qual coluna e tipo de dados está presente no conjunto de dados de origem, você pode alterar os campos RangeStart e RangeEnd adequadamente

   
   |Propriedade  |Tipo de dados  |Filtro  |
   |---------|---------|---------|
   |_ts     |   Numeric      |  [_ts] > Duration. TotalSeconds (RangeStart-#datetime (1970, 1, 1, 0, 0, 0)) e [_ts] < Duration. TotalSeconds (RangeEnd-#datetime (1970, 1, 1, 0, 0, 0)))       |
   |Data (por exemplo:-2019-08-19)     |   Cadeia      | [Document. Date] > DateTime. totext (RangeStart, "YYYY-MM-DD") e [Document. Date] < DateTime. totext (RangeEnd, "aaaa-MM-DD")        |
   |Data (por exemplo:-2019-08-11 12:00:00)   |  Cadeia       |  [Document. Date] > DateTime. totext (RangeStart, "aaaa-mm-dd HH: mm: SS") e [Document. Date] < DateTime. totext (RangeEnd, "aaaa-mm-dd HH: mm: SS")       |


1. **Definir a política de atualização** – defina a política de atualização navegando até a guia **atualização incremental** no menu de **contexto** da tabela. Defina a política de atualização para atualizar **todos os dias** e armazene os dados do último mês.

   ![Definir política de atualização](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   Ignore o aviso dizendo que *a consulta M não pode ser confirmada para ser dobrada*. O conector de Azure Cosmos DB dobra as consultas de filtro.

1. **Carregue os dados e gere os relatórios** -usando os dados que você carregou anteriormente, crie os gráficos para relatar a temperatura e chuva.

   ![Carregar dados e gerar relatório](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Publicar o relatório para Power bi Premium** -como a atualização incremental é um recurso somente Premium, a caixa de diálogo publicar só permite a seleção de um espaço de trabalho na capacidade Premium. A primeira atualização pode levar mais tempo para importar os dados históricos. As atualizações de dados subsequentes são muito mais rápidas porque usam a atualização incremental.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI o conector de análise do Azure + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingerir dados meteorológicos em Azure Cosmos DB 

Configure um pipeline de ingestão para carregar [dados meteorológicos](https://catalog.data.gov/dataset/local-weather-archive) para Azure Cosmos DB. Você pode configurar um trabalho de Azure Data Factory (ADF) para carregar periodicamente os dados meteorológicos mais recentes em Azure Cosmos DB usando o coletor de Cosmos DB e de origem HTTP.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Conectar Azure Analysis Services à conta do cosmos do Azure

1. **Criar um novo cluster** - [de Azure Analysis Services criar uma instância do Azure Analysis Services](../analysis-services/analysis-services-create-server.md) na mesma região que a conta do Azure Cosmos e o cluster do databricks.

1. **Crie um novo projeto de tabela Analysis Services no Visual Studio** -  [Instale o SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) e crie um projeto de tabela Analysis Services no Visual Studio.

   ![Criar Azure Analysis Services projeto](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   Escolha a instância do **espaço de trabalho integrado** e defina o nível de compatibilidade para **SQL Server 2017/Azure Analysis Services (1400)**

   ![Designer de modelo de tabela Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Adicionar a fonte de dados Azure Cosmos DB** -navegue até **modelos**> **fontes** > de dados**nova fonte de dados** e adicione a fonte de dados Azure Cosmos DB, conforme mostrado na seguinte captura de tela:

   ![Adicionar fonte de dados de Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   Conecte-se a Azure Cosmos DB fornecendo o **URI da conta**, o nome do banco de **dados**e o nome do **contêiner**. Agora você pode ver que os dados do contêiner Cosmos do Azure são importados para o Power BI.

   ![Visualizar dados de Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Construir o modelo de Analysis Services** – Abra o editor de consultas, execute as operações necessárias para otimizar o conjunto de dados carregado:

   * Extrair apenas as colunas relacionadas ao clima (temperatura e chuva)

   * Extraia as informações de mês da tabela. Esses dados são úteis na criação de partições, conforme descrito na próxima seção.

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

   Além disso, altere o tipo de dados das colunas de temperatura para decimal para certificar-se de que esses valores podem ser plotados em Power BI.

1. **Criar partições de análise do Azure** – crie partições no Azure Analysis Services para dividir o conjunto de os em partições lógicas que podem ser atualizadas de forma independente e em frequências diferentes. Neste exemplo, você cria duas partições que dividiria o conjunto de dados nos mais recentes e em todo o restante do mês.

   ![Criar partições do Analysis Services](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Crie as duas partições a seguir no Azure Analysis Services:

   * **Último mês** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Histórico** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Implante o modelo no Azure Analysis Server** -clique com o botão direito do mouse no projeto Azure Analysis Services e escolha **implantar**. Adicione o nome do servidor no painel **Propriedades do servidor de implantação** .

   ![Implantar modelo de Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Configurar atualizações de partição e mesclagens** -Azure Analysis Services permite o processamento independente de partições. Como queremos que a partição do **mês mais recente** seja constantemente atualizada com os dados mais recentes, defina o intervalo de atualização para 5 minutos. Não é necessário atualizar os dados na partição histórica. Além disso, você precisa escrever algum código para consolidar a partição de mês mais recente na partição histórica e criar uma nova partição de mês mais recente.


## <a name="connect-power-bi-to-analysis-services"></a>Conectar Power BI ao Analysis Services

1. **Conecte-se ao Analysis Server do Azure usando o conector de banco de dados Azure Analysis Services** -escolha o **modo dinâmico** e conecte-se à instância de Azure Analysis Services, conforme mostrado na seguinte captura de tela:

   ![Obter dados de Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Carregar os dados e gerar relatórios** -usando os dados que você carregou anteriormente, crie gráficos para relatar a temperatura e a chuva. Como você está criando uma conexão dinâmica, as consultas devem ser executadas nos dados no modelo de Azure Analysis Services que você implantou na etapa anterior. Os gráficos de temperatura serão atualizados em cinco minutos depois que os novos dados forem carregados no Azure Cosmos DB.

   ![Carregar os dados e gerar relatórios](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre Power BI, consulte Introdução [ao Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Conecte o Qlik Sense Sense ao Azure Cosmos DB e visualize seus dados](visualize-qlik-sense.md)