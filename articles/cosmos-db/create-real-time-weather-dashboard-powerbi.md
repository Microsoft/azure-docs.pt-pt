---
title: Crie um dashboard em tempo real utilizando o Azure Cosmos DB, os Serviços de Análise Azure e o Power BI
description: Aprenda a criar um dashboard meteorológico ao vivo em Power BI utilizando os Serviços de Análise Azure Cosmos DB e Azure.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70376596"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Crie um dashboard em tempo real usando O Azure Cosmos DB e Power BI

Este artigo descreve os passos necessários para criar um dashboard de tempo vivo em Power BI utilizando os Serviços de Análise Azure Cosmos DB e Azure. O painel power bi apresentará gráficos para mostrar informações em tempo real sobre temperatura e chuva numa região.

## <a name="reporting-scenarios"></a>Cenários de reporte

Existem várias formas de configurar dashboards de reporte de dados armazenados em Azure Cosmos DB. Dependendo dos requisitos de estagnação e da dimensão dos dados, o quadro seguinte descreve a configuração do relatório para cada cenário:


|Cenário |Configuração |
|---------|---------|
|1. Gerar relatórios ad-hoc (sem atualização)    |  [Conector Power BI Azure Cosmos DB com modo de importação](powerbi-visualize.md)       |
|2. Gerar relatórios ad-hoc com atualização periódica   |  [Conector Power BI Azure Cosmos DB com modo de importação (atualização periódica programada)](powerbi-visualize.md)       |
|3. Relatórios sobre grandes conjuntos de dados (< 10 GB)     |  Conector Power BI Azure Cosmos DB com atualização incremental       |
|4. Reportar em tempo real em grandes conjuntos de dados    |  Conector power BI Azure Analysis Services com consulta direta + Serviços de Análise Azure (conector Azure Cosmos DB)       |
|5. Reportar dados ao vivo com agregados     |  [Conector Power BI Spark com consulta direta + Azure Databricks + Cosmos DB Spark connector.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Reportar dados vivos com agregados em grandes conjuntos de dados   |  Power BI Azure Analysis Services conector com consulta direta + Serviços de Análise Azure + Azure Databricks + Cosmos DB Spark conector.       |

Os cenários 1 e 2 podem ser facilmente configurados utilizando o conector Azure Cosmos DB Power BI. Este artigo descreve as configurações para os cenários 3 e 4.

### <a name="power-bi-with-incremental-refresh"></a>Power BI com atualização incremental

O Power BI tem um modo em que a atualização incremental pode ser configurada. Este modo elimina a necessidade de criar e gerir divisórias dos Serviços de Análise Azure. A atualização incremental pode ser configurada para filtrar apenas as últimas atualizações em grandes conjuntos de dados. No entanto, este modo funciona apenas com o serviço Power BI Premium que tem uma limitação de conjunto de dados de 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Conector power BI Azure Analysis + Serviços de Análise Azure

O Azure Analysis Services fornece uma plataforma totalmente gerida como um serviço que acolhe modelos de dados de nível empresarial na nuvem. Conjuntos de dados maciços podem ser carregados de Azure Cosmos DB para Serviços de Análise Azure. Para evitar a consulta de todo o conjunto de dados a toda a hora, os conjuntos de dados podem ser subdivididos em divisórias dos Serviços de Análise Azure, que podem ser refrescadas de forma independente em diferentes frequências.

## <a name="power-bi-incremental-refresh"></a>Atualização incremental power BI

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingerir dados meteorológicos no Azure Cosmos DB

Instale um oleoduto de ingestão para carregar [dados meteorológicos](https://catalog.data.gov/dataset/local-weather-archive) para o Azure Cosmos DB. Pode configurar um trabalho da [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) para carregar periodicamente os últimos dados meteorológicos no Azure Cosmos DB utilizando a pia HTTP Source e Cosmos DB.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Ligue power BI ao Azure Cosmos DB

1. Ligue a **conta Azure Cosmos ao Power BI** - Abra o Power BI Desktop e utilize o conector Azure Cosmos DB para selecionar a base de dados e o recipiente certos.

   ![Conector do Power BI do Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Configurar** a atualização incremental - Siga os passos em [atualização incremental com](/power-bi/service-premium-incremental-refresh) o artigo power BI para configurar a atualização incremental para o conjunto de dados. Adicione os parâmetros **RangeStart** e **RangeEnd,** como mostra a seguinte imagem:

   ![Configurar parâmetros de gama](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Uma vez que o conjunto de dados tem uma coluna Data que está em forma de texto, os parâmetros **RangeStart** e **RangeEnd** devem ser transformados para utilizar o seguinte filtro. No painel **do Editor Avançado,** modifique a sua consulta adicione o seguinte texto para filtrar as linhas com base nos parâmetros RangeStart e RangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   Dependendo do tipo de coluna e de dados presente no conjunto de dados de origem, pode alterar os campos RangeStart e RangeEnd em conformidade

   
   |Propriedade  |Tipo de dados  |Filtro  |
   |---------|---------|---------|
   |_ts     |   Numérico      |  [_ts] > Duração.TotalSeconds(RangeStart - #datetime(1970, 1, 1, 0, 0, 0)) e [_ts] < Duração.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0, 0))       |
   |Data (por exemplo:- 2019-08-19)     |   Cadeia      | [Documento.data]> DateTime.ToText(RangeStart",yyyy-MM-dd") e [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd")        |
   |Data (por exemplo:- 2019-08-11 12:00:00)   |  Cadeia       |  [Documento.data]> DateTime.ToText(RangeStart", yyyy-mm-dd HH:mm:ss") e [Document.date] < DateTime.ToText(RangeEnd,"yyyy-mm-dd HH:mm:ss")       |


1. **Defina a política** de atualização - Defina a política de atualização navegando para o **separador de atualização Incremental** no menu de **contexto** para a tabela. Desadote a política de atualização para atualizar **todos os dias** e armazenar os dados do último mês.

   ![Definir política de atualização](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   Ignore o aviso que diz que *a consulta M não pode ser confirmada para ser dobrada*. O conector Azure Cosmos DB dobra consultas de filtro.

1. **Carregue os dados e gere os relatórios** - Utilizando os dados que carregou anteriormente, crie os gráficos para reportar sobre temperatura e chuva.

   ![Carregar dados e gerar relatório](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Publique o relatório ao Power BI premium** - Uma vez que a atualização incremental é uma funcionalidade premium única, o diálogo de publicação só permite a seleção de um espaço de trabalho na capacidade Premium. A primeira atualização poderá demorar mais tempo a importar os dados do histórico. As atualizações de dados subsequentes são muito mais rápidas porque utilizam a atualização incremental.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Conector power BI Azure Analysis + Serviços de Análise Azure 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingerir dados meteorológicos no Azure Cosmos DB 

Instale um oleoduto de ingestão para carregar [dados meteorológicos](https://catalog.data.gov/dataset/local-weather-archive) para o Azure Cosmos DB. Pode configurar um trabalho da Azure Data Factory (ADF) para carregar periodicamente os últimos dados meteorológicos no Azure Cosmos DB utilizando o HTTP Source e cosmos DB Sink.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Ligue os Serviços de Análise Azure à conta Azure Cosmos

1. **Criar um novo cluster** - de Serviços de Análise Azure[Criar uma instância de serviços de Análise Azure](../analysis-services/analysis-services-create-server.md) na mesma região que a conta Azure Cosmos e o cluster Databricks.

1. **Crie um novo Projeto Tabular de Serviços de Análise em Estúdio** -  Visual Instale as Ferramentas de Dados do[Servidor SQL (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) e crie um projeto tabular de serviços de análise em Estúdio Visual.

   ![Criar projeto de Serviços de Análise Azure](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   Escolha a instância integrada do **espaço de trabalho** e o conjunto do Nível de Compatibilidade para os **Serviços de Análise SQL 2017 / Azure (1400)**

   ![Azure Analysis Services tabular designer modelo](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Adicione a fonte de dados do Azure Cosmos DB** - Navegue para **os modelos**> **Fonte de** > **Dados Novas Fontes** de Dados e adicione a fonte de dados do Azure Cosmos DB, como mostra a seguinte imagem:

   ![Adicionar fonte de dados cosmos DB](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   Ligue-se ao Azure Cosmos DB fornecendo a **conta URI,** nome da base de **dados,** e o nome do **recipiente**. Pode agora ver que os dados do contentor Azure Cosmos são importados para o Power BI.

   ![Pré-visualização dados do Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Construa o modelo de Serviços** de Análise - Abra o editor de consulta, execute as operações necessárias para otimizar o conjunto de dados carregado:

   * Extrair apenas as colunas relacionadas com o tempo (temperatura e chuva)

   * Extrai a informação do mês da mesa. Estes dados são úteis na criação de divisórias como descrito na secção seguinte.

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

1. **Create Azure Analysis partitions** - Crie divisórias em Serviços de Análise Azure para dividir o conjunto de dados em divisórias lógicas que podem ser refrescadas de forma independente e em diferentes frequências. Neste exemplo, cria-se duas divisórias que dividiriam o conjunto de dados nos dados mais recentes e em tudo o resto.

   ![Criar divisórias de serviços de análise](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Crie as seguintes duas divisórias nos Serviços de Análise Azure:

   * **Último mês** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Histórico** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Implemente o Modelo para o Servidor** de Análise Azure - Clique no clique certo no projeto Dos Serviços de Análise Do Azure e escolha **implementar**. Adicione o nome do servidor no painel de propriedades do **Servidor de Implementação.**

   ![Implementar modelo de Serviços de Análise Azure](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Configurar a atualização e fusão** da partilha - Os Serviços de Análise Azure permitem o processamento independente de divisórias. Uma vez que queremos que a partição do **último mês** seja constantemente atualizada com os dados mais recentes, detetete o intervalo de atualização para 5 minutos. Não é necessário refrescar os dados em divisóriahistórica. Além disso, você precisa escrever algum código para consolidar a partição do último mês para a partição histórica e criar uma nova partição do último mês.


## <a name="connect-power-bi-to-analysis-services"></a>Ligar Power BI aos Serviços de Análise

1. **Ligue-se ao Servidor** de Análise Azure utilizando a base de dados dos Serviços de Análise Azure Connector - Escolha o **modo Live** e ligue-se à instância dos Serviços de Análise Azure, como mostra a seguinte imagem:

   ![Obtenha dados dos Serviços de Análise Azure](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Carregue os dados e gere relatórios** - Ao utilizar os dados que carregou anteriormente, crie gráficos para reportar sobre temperatura e chuva. Uma vez que está a criar uma ligação ao vivo, as consultas devem ser executadas nos dados do modelo de Serviços de Análise Azure que implementou no passo anterior. Os gráficos de temperatura serão atualizados dentro de cinco minutos após a entrada dos novos dados no Azure Cosmos DB.

   ![Carregue os dados e gere relatórios](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o Power BI, consulte [Começar com Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Ligue qlik Sense ao Azure Cosmos DB e visualize os seus dados](visualize-qlik-sense.md)