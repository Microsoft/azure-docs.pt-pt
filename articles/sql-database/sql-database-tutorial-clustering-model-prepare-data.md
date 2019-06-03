---
title: 'Tutorial: Preparar dados para efetuar o clustering no R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na primeira parte desta série de tutoriais de três partes, irá preparar os dados a partir de uma base de dados SQL do Azure para executar o clustering no R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização).
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
ms.openlocfilehash: 83ef25f04012933c2665e63e4617d480eb336f7b
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419462"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Preparar dados para efetuar o clustering no R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)

Na primeira parte desta série de tutoriais de três partes, irá preparar os dados a partir de uma base de dados SQL do Azure para executar o clustering no R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização).

*Clustering* pode ser explicado como organizar os dados em grupos em que os membros de um grupo são semelhantes de alguma forma.
Irá utilizar o **K-Means** algoritmo para efetuar o clustering de clientes num conjunto de dados do produto de compras e retorna. Agrupando os clientes, pode se concentrar seus esforços de marketing com mais eficiência ao filtrar grupos específicos.
Clustering de K-Means é um *learning não supervisionado* algoritmo que procura por padrões nos dados com base nas similaridades.

Neste artigo, ficará a saber como:

> [!div class="checklist"]
> * Importar uma base de dados de exemplo para uma base de dados SQL do Azure
> * Clientes separados ao longo de dimensões diferentes
> * Carregar os dados da base de dados SQL do Azure para um quadro de dados através da linguagem R

Na [parte dois](sql-database-tutorial-clustering-model-build.md), ficará a saber como criar e formar um modelo de clustering de K-Means.

Na [parte três](sql-database-tutorial-clustering-model-deploy.md), ficará a saber como criar um procedimento armazenado numa base de dados SQL do Azure que pode executar o clustering com base nos dados de novo.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure - se não tiver uma subscrição do Azure, [criar uma conta](https://azure.microsoft.com/free/) antes de começar.

* Servidor de base de dados SQL do Azure com o Machine Learning Services habilitado - durante a pré-visualização pública, será de Microsoft carregar e ativar machine learning para seus bancos de dados novos ou existentes. Siga os passos em [Inscreva-se na pré-visualização](sql-database-machine-learning-services-overview.md#signup).

* Pacote de RevoScaleR - consulte [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) para conhecer as opções instalar este pacote localmente.

* Este tutorial utiliza o IDE de R - [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Ferramenta de consulta SQL - este tutorial pressupõe que está a utilizar [Estúdio de dados do Azure](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importar a base de dados de exemplo

O conjunto de dados de exemplo utilizado neste tutorial foi guardado para uma **. bacpac** ficheiro de cópia de segurança da base de dados que pode transferir e utilizar. Este conjunto de dados é derivado do [tpcx bb](http://www.tpc.org/tpcx-bb/default.asp) conjunto de dados fornecido pela [Conselho de desempenho de processamento ' (TPC) da transação](http://www.tpc.org/default.asp).

1. Transfira o ficheiro [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Siga as indicações [importar um ficheiro BACPAC para criar uma base de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-import), com estes detalhes:

   * Importar a partir da **tpcxbb_1gb.bacpac** arquivo que baixou
   * Durante a pré-visualização pública, escolha o **vcore Gen5/da** configuração para a nova base de dados
   * Nomeie a nova base de dados "tpcxbb_1gb"

## <a name="separate-customers"></a>Clientes separados

Criar um novo ficheiro de RScript no r Studio e execute o seguinte script.
Na consulta SQL, está sendo separada clientes juntamente com as seguintes dimensões:

* **orderRatio** = rácio de ordem de retorno (o número total de pedidos total ou parcialmente devolvido em comparação com o número total de pedidos)
* **itemsRatio** = rácio de item de retorno (o número total de itens retornados em comparação com o número de itens comprados)
* **monetaryRatio** = rácio de valor de retorno (montante monetário total de itens retornados em comparação com a quantidade comprada)
* **frequência** = frequência de retorna

Na **colar** funcionar, substitua **servidor**, **UID**, e **PWD** pelas suas informações de ligação.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE 
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE 
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE 
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE 
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
"
```

## <a name="load-the-data-into-a-data-frame"></a>Carregar os dados para um quadro de dados

Agora, utilize o seguinte script para devolver os resultados da consulta para um data de R quadro com o **rxSqlServerData** função.
Como parte do processo, definirá o tipo para as colunas selecionadas (usando colClasses) para se certificar de que os tipos corretamente são transferidos para R.

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

Deverá ver resultados semelhantes ao seguinte.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Limpar recursos

***Se não pretender continuar com este tutorial***, eliminar a base de dados tpcxbb_1gb do seu servidor de base de dados do Azure SQL.

No portal do Azure, siga estes passos:

1. No menu esquerdo no portal do Azure, selecione **todos os recursos** ou **bases de dados SQL**.
1. Na **filtrar por nome...**  , insira **tpcxbb_1gb**e selecione a sua subscrição.
1. Selecione seu **tpcxbb_1gb** base de dados.
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Na primeira parte desta série de tutoriais, concluiu estes passos:

* Importar uma base de dados de exemplo para uma base de dados SQL do Azure
* Clientes separados ao longo de dimensões diferentes
* Carregar os dados da base de dados SQL do Azure para um quadro de dados através da linguagem R

Para criar um modelo de machine learning que utiliza estes dados de cliente, siga a parte dois desta série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Criar um modelo preditivo em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)](sql-database-tutorial-clustering-model-build.md)
