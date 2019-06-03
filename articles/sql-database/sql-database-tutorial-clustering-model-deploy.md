---
title: 'Tutorial: Implementar um modelo de clustering em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na terceira parte desta série de tutoriais de três partes, vai implementar um modelo de clustering em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização).
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
ms.date: 05/17/2019
ms.openlocfilehash: 1fe9df6378884ba55cb1017da87522ae66edaff0
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419756"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Implementar um modelo de clustering em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)

Na terceira parte desta série de tutoriais de três partes, vai implementar um modelo de clustering em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização).

Irá criar um procedimento armazenado com um script R incorporado, que executa o clustering. Como seu modelo é executado na base de dados SQL do Azure, pode facilmente ser treinado em relação a dados armazenados na base de dados.

Neste artigo, ficará a saber como:

> [!div class="checklist"]
> * Criar um procedimento armazenado que gera o modelo
> * Executar o clustering na base de dados SQL
> * Utilize as informações de clusters

Na [parte um](sql-database-tutorial-clustering-model-prepare-data.md), ficou a saber como preparar os dados de uma base de dados SQL do Azure para executar o clustering em R.

Na [parte dois](sql-database-tutorial-clustering-model-build.md), aprendeu a criar um modelo de K-Means para executar o clustering.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Terceira parte desta série de tutoriais pressupõe que concluiu [ **parte um** ](sql-database-tutorial-clustering-model-prepare-data.md) e [ **parte dois**](sql-database-tutorial-clustering-model-build.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Criar um procedimento armazenado que gera o modelo

Execute o seguinte script de T-SQL para criar o procedimento armazenado. O procedimento recria as etapas desenvolvidas em partes um e dois desta série de tutoriais:

* os clientes com base em compras de classificar e retornar o histórico
* gerar quatro clusters de clientes que utilizam um algoritmo K-Means

O procedimento armazena os mapeamentos de cluster de cliente resultante da tabela de base de dados **customer_return_clusters**.

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

## <a name="perform-clustering-in-sql-database"></a>Executar o clustering na base de dados SQL

Agora que criou o procedimento armazenado, execute o seguinte script para executar o clustering.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Certifique-se de que ele funciona e que, na verdade, temos a lista de clientes e os respetivos mapeamentos de cluster.

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

## <a name="use-the-clustering-information"></a>Utilize as informações de clusters

Uma vez que o clustering procedimento armazenado na base de dados, pode efetuar clustering com eficiência em relação a dados de clientes armazenados no mesmo banco de dados. Pode executar o procedimento sempre que os dados dos clientes são atualizados e utilizam as informações atualizadas de clusters.

Suponha que deseja enviar uma mensagem de e-mail promocional para os clientes no cluster 3, o grupo que tenha o comportamento de retorno mais ativo (pode ver como foram descritos quatro clusters [parte dois](sql-database-tutorial-clustering-model-build.md#analyze-the-results)). O código a seguir seleciona os endereços de e-mail de clientes num cluster de 3.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Pode alterar o **r.cluster** valor a devolver endereços de e-mail para clientes nos outros clusters.

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com este tutorial, pode eliminar a base de dados tpcxbb_1gb do seu servidor de base de dados do Azure SQL.

No portal do Azure, siga estes passos:

1. No menu esquerdo no portal do Azure, selecione **todos os recursos** ou **bases de dados SQL**.
1. Na **filtrar por nome...**  , insira **tpcxbb_1gb**e selecione a sua subscrição.
1. Selecione seu **tpcxbb_1gb** base de dados.
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Na terceira parte desta série de tutoriais, concluiu estes passos:

* Criar um procedimento armazenado que gera o modelo
* Executar o clustering na base de dados SQL
* Utilize as informações de clusters

Para saber mais sobre como utilizar o R no Machine Learning Services (pré-visualização) do banco de dados do Azure SQL, veja:

* [Tutorial: Preparar dados para preparar um modelo preditivo em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)](sql-database-tutorial-predictive-model-prepare-data.md)
* [Escrever funções avançadas do R na base de dados do SQL Azure com serviços do Machine Learning (pré-visualização)](sql-database-machine-learning-services-functions.md)
* [Trabalhar com dados de R e SQL no SQL da base de dados serviços Azure Machine Learning (pré-visualização)](sql-database-machine-learning-services-data-issues.md)
* [Adicionar um pacote de R para serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)](sql-database-machine-learning-services-add-r-packages.md)
