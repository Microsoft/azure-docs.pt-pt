---
title: Power BI e piscina SQL sem servidor para analisar dados DB do Azure Cosmos com Synapse Link
description: Aprenda a construir uma base de dados de piscinas SQL sem servidor e vistas sobre a Synapse Link para Azure Cosmos DB, consultar os contentores DB do Azure Cosmos e, em seguida, construir um modelo com Power BI sobre essas vistas.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: synapse-cosmos-db
ms.openlocfilehash: d84508a7629481a7138f1080c86f4a203d35894d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626253"
---
# <a name="use-power-bi-and-serverless-synapse-sql-pool-to-analyze-azure-cosmos-db-data-with-synapse-link"></a>Use power BI e sem servidor Piscina SQL synapse para analisar dados DB do Azure Cosmos com Synapse Link 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Neste artigo, você aprende a construir uma base de dados de piscinas SQL sem servidor e vistas sobre Synapse Link para Azure Cosmos DB. Você vai consultar os recipientes DB Azure Cosmos e, em seguida, construir um modelo com Power BI sobre essas vistas para refletir essa consulta.

Neste cenário, utilizará dados falsos sobre as vendas de produtos Surface numa loja de retalho parceira. Irá analisar as receitas por loja com base na proximidade com as grandes famílias e no impacto da publicidade para uma semana específica. Neste artigo, cria **duas vistas chamadas RetailSales** e **StoreDemographics** e uma consulta entre eles. Pode obter os dados do produto da amostra deste [repo GitHub.](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData)

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de criar os seguintes recursos antes de começar:

* [Crie uma conta DB Azure Cosmos do tipo SQL (core) ou MongoDB.](create-cosmosdb-resources-portal.md)

* Ativar o Link Azure Synapse para a sua [conta Azure Cosmos](configure-synapse-link.md#enable-synapse-link)

* Crie uma base de dados dentro da conta Azure Cosmos e dois contentores que tenham [a loja analítica ativada.](configure-synapse-link.md#create-analytical-ttl)

* Carregue os dados dos produtos nos recipientes Azure Cosmos, conforme descrito neste caderno [de ingestão de dados do lote.](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb)

* [Crie um espaço de trabalho synapse](../synapse-analytics/quickstart-create-workspace.md) chamado **SynapseLinkBI**.

* [Ligue a base de dados Azure Cosmos ao espaço de trabalho da Sinapse.](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)

## <a name="create-a-database-and-views"></a>Criar uma base de dados e vistas

A partir do espaço de trabalho Synapse vá o **separador Desenvolver,** selecione o **+** ícone e selecione **O Script SQL**.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Adicione um script SQL ao espaço de trabalho Synapse Analytics":::

Cada espaço de trabalho vem com um ponto final SQL sem servidor. Depois de criar um script SQL, a partir da barra de ferramentas na parte superior **conecte-se** a Incorporado .

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="Ative o script SQL para usar o ponto final SQL sem servidor no espaço de trabalho":::

A criação de pontos de vista nas bases de dados **principais** ou **predefinidas** não é recomendada ou suportada. Crie uma nova base de dados, denominada **RetailCosmosDB,** e uma vista SQL sobre os recipientes ativados pela Synapse Link. O seguinte comando mostra como criar uma base de dados:

```sql
-- Create database
Create database RetailCosmosDB
```

Em seguida, crie múltiplas vistas em diferentes recipientes Synapse Link habilitados Azure Cosmos. As vistas permitir-lhe-ão utilizar o T-SQL para se juntar e consultar dados do Azure Cosmos DB sentados em diferentes contentores.  Certifique-se de selecionar a base **de dados RetailCosmosDB** ao criar as vistas.

Os seguintes scripts mostram como criar vistas em cada recipiente. Para simplificar, vamos utilizar a [função automática](analytical-store-introduction.md#analytical-schema) de inferência de esquema da piscina SQL sem servidor sobre os recipientes ativados synapse Link:


### <a name="retailsales-view"></a>Vista RetailSales:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

Certifique-se de inserir a sua região DB Azure Cosmos e a chave primária no script SQL anterior. Todos os caracteres da região devem estar em minúsculas sem espaços. Ao contrário dos outros parâmetros do `OPENROWSET` comando, o parâmetro do nome do recipiente deve ser especificado sem citações à sua volta.

### <a name="storedemographics-view"></a>Vista Demográficas do Store:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

Agora, executar o script SQL selecionando o comando **Executar.**

## <a name="query-the-views"></a>Consultar as vistas

Agora que as duas vistas são criadas, vamos definir a consulta para juntar estas duas vistas da seguinte forma:

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

Selecione **Executar** que dá a seguinte tabela como resultado:

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="Resultados de consulta após a adesão às vistas StoreDemographics e RetailSales":::

## <a name="model-views-over-containers-with-power-bi"></a>Vistas do modelo sobre contentores com Power BI

Em seguida, abra o ambiente de trabalho Power BI e ligue-se ao ponto final SQL sem servidor utilizando os seguintes passos:

1. Abra a aplicação Power BI Desktop. **Selecione Obter dados** e selecione **mais**.

1. Escolha **Azure Synapse Analytics (SQL DW)** na lista de opções de conexão.

1. Insira o nome do ponto final SQL onde a base de dados está localizada. Introduza `SynapseLinkBI-ondemand.sql.azuresynapse.net` dentro do campo **Servidor.** Neste exemplo,  **o SynapseLinkBI** é o nome do espaço de trabalho. Substitua-o se tiver dado um nome diferente ao seu espaço de trabalho. Selecione **Consulta Direta** para o modo de conectividade de dados e, em seguida, **OK**.

1. Selecione o método de autenticação preferido, como Azure AD.

1. Selecione a base **de dados RetailCosmosDB** e as **vistas RetailSales,** **StoreDemographics.**

1. Selecione **Carregar** para carregar as duas vistas no modo de consulta direta.

1. Selecione **Modelo** para criar uma relação entre as duas vistas através da coluna **storeId.**

1. Arraste a coluna **StoreId** da vista **RetailSales** para a coluna **StoreId** na vista **StoreDemographics.**

1. Selecione a relação Many to one (*:1) porque existem várias linhas com o mesmo ID de loja na vista **RetailSales.** **StoreDemographics** tem apenas uma linha de ID de loja (é uma tabela de dimensão).

Agora navegue para a janela do **relatório** e crie um relatório para comparar a importância relativa do tamanho das famílias com a receita média por loja com base na representação dispersa das receitas e no índice LargeHH:

1. Selecione **gráfico de dispersão**.

1. Arraste e deixe **cair o LargeHH** da vista **StoreDemographics** para o eixo X.

1. Arrastar e largar **receitas** do **RetailSales** ver para o eixo Y. Selecione **Average** para obter a média de vendas por produto por loja e por semana.

1. Arraste e deixe cair o **produto A** partir da visão **retailSales** para a lenda para selecionar uma linha de produto específica.
Depois de escolher estas opções, deverá ver um gráfico como a seguinte imagem:

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="Relatório que compara a importância relativa do tamanho das famílias com a receita média por loja":::

## <a name="next-steps"></a>Passos seguintes

[Utilize t-SQL para consultar dados DB da Azure Cosmos utilizando a Ligação Azure Synapse](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

Use piscina SQL sem servidor para [analisar conjuntos de dados Azure Open e visualizar os resultados no Azure Synapse Studio](../synapse-analytics/sql/tutorial-data-analyst.md)
