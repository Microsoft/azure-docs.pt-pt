---
title: 'Tutorial: Implementar um modelo de clustering em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na terceira parte desta série tutorial em três partes, irá sacar um modelo de clustering em R com o Azure SQL Database Machine Learning Services (pré-visualização).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ef478246108d40a0c97d7dab03ecf1e5b474410b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452898"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Implementar um modelo de clustering em R com Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)

Na terceira parte desta série tutorial em três partes, irá implantar um modelo de clustering, desenvolvido em R, numa base de dados SQL utilizando os Serviços de Machine Learning de Máquinas de Base de Dados Azure SQL (pré-visualização).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Você vai criar um procedimento armazenado com um script R incorporado que executa clustering. Como o seu modelo executa na base de dados Azure SQL, pode ser facilmente treinado contra dados armazenados na base de dados.

Neste artigo, aprenderá a:

> [!div class="checklist"]
> * Criar um procedimento armazenado que gere o modelo
> * Executar clustering na Base de Dados SQL
> * Utilize a informação de agrupamento

Na [primeira parte,](sql-database-tutorial-clustering-model-prepare-data.md)aprendeu a preparar os dados a partir de uma base de dados Azure SQL para realizar o agrupamento.

Na [segunda parte,](sql-database-tutorial-clustering-model-build.md)aprendeste a criar e treinar um modelo de agrupamento K-Means em R.

## <a name="prerequisites"></a>Pré-requisitos

* A terceira parte desta série tutorial pressupõe que tenha completado a [**parte um**](sql-database-tutorial-clustering-model-prepare-data.md) e a [**segunda parte.**](sql-database-tutorial-clustering-model-build.md)

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Criar um procedimento armazenado que gere o modelo

Executar o seguinte script T-SQL para criar o procedimento armazenado. O procedimento recria os passos que desenvolveu nas partes um e dois desta série tutorial:

* classificar os clientes com base no seu histórico de compra e devolução
* gerar quatro clusters de clientes usando um algoritmo K-Means

O procedimento armazena os mapeamentos de cluster de clientes resultantes na tabela de **dados customer_return_clusters**.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
            WHEN (returns_count IS NULL)
                THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>Executar clustering na Base de Dados SQL

Agora que criou o procedimento armazenado, execute o seguinte guião para executar o agrupamento.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Verifique se funciona e que temos realmente a lista de clientes e os seus mapeamentos de cluster.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>Utilize a informação de agrupamento

Como armazenou o procedimento de clustering na base de dados, pode realizar um agrupamento de forma eficiente contra os dados do cliente armazenados na mesma base de dados. Pode executar o procedimento sempre que os dados do seu cliente forem atualizados e utilizar as informações de clusteratualizadas atualizadas.

Suponha que queira enviar um e-mail promocional aos clientes do cluster 3, o grupo que tem um comportamento de retorno mais ativo (pode ver como os quatro clusters foram descritos na [segunda parte](sql-database-tutorial-clustering-model-build.md#analyze-the-results)). O código seguinte seleciona os endereços de e-mail dos clientes no cluster 3.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Pode alterar o valor do **r.cluster** para devolver endereços de e-mail para clientes de outros clusters.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar este tutorial, pode eliminar a base de dados tpcxbb_1gb do seu servidor de base de dados Azure SQL.

A partir do portal Azure, siga estes passos:

1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** ou bases de dados **SQL**.
1. No **campo Filter por nome...** introduza **tpcxbb_1gb,** e selecione a sua subscrição.
1. Selecione a sua base de dados **tpcxbb_1gb.**
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Na terceira parte desta série tutorial, completou estes passos:

* Criar um procedimento armazenado que gere o modelo
* Executar clustering na Base de Dados SQL
* Utilize a informação de agrupamento

Para saber mais sobre a utilização de R em Azure SQL Database Machine Learning Services (pré-visualização), consulte:

* [Tutorial: Prepare dados para treinar um modelo preditivo em R com serviços de machine learning de base de dados Azure SQL (pré-visualização)](sql-database-tutorial-predictive-model-prepare-data.md)
* [Escreva funções R avançadas na Base de Dados Azure SQL utilizando serviços de aprendizagem automática (pré-visualização)](sql-database-machine-learning-services-functions.md)
* [Trabalhar com dados R e SQL nos Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)](sql-database-machine-learning-services-data-issues.md)
* [Adicione um pacote R aos Serviços de Machine Learning de Máquinas de Base de Dados Azure SQL (pré-visualização)](sql-database-machine-learning-services-add-r-packages.md)
