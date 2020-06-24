---
title: 'Tutorial: Preparar dados para realizar agrupamentos em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na primeira parte desta série tutorial em três partes, irá preparar os dados de uma base de dados na Base de Dados Azure SQL para realizar o agrupamento em R com Azure SQL Database Machine Learning Services (pré-visualização).
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
ms.openlocfilehash: a23dbd150dbe8ab05e0d4cf1f3decd67a856cbf4
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85251255"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Preparar dados para realizar agrupamentos em R com Azure SQL Database Machine Learning Services (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Na primeira parte desta série tutorial em três partes, importará e preparará os dados de uma base de dados na Base de Dados Azure SQL utilizando R. Mais tarde nesta série, você usará estes dados para treinar e implementar um modelo de clustering em R com Azure SQL Database Machine Learning Services (pré-visualização).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

*O agrupamento* pode ser explicado como organizar dados em grupos onde os membros de um grupo são semelhantes de alguma forma.
Você usará o algoritmo **K-Means** para realizar o agrupamento de clientes num conjunto de dados de compras e devoluções de produtos. Ao clustering clientes, você pode concentrar os seus esforços de marketing de forma mais eficaz, direcionando grupos específicos.
O clustering K-Means é um algoritmo *de aprendizagem não supervisionado* que procura padrões em dados baseados em semelhanças.

Nas partes um e dois desta série, você vai desenvolver alguns scripts R em RStudio para preparar os seus dados e treinar um modelo de machine learning. Depois, na terceira parte, executará os scripts R dentro da base de dados usando procedimentos armazenados.

Neste artigo, aprenderá a:

> [!div class="checklist"]
>
> * Importar uma base de dados de amostras para a Base de Dados Azure SQL
> * Clientes separados ao longo de diferentes dimensões usando R
> * Carregue os dados da base de dados num quadro de dados R

Na [segunda parte,](clustering-model-build-tutorial.md)você vai aprender a criar e treinar um modelo de clustering K-Means em R.

Na [terceira parte,](clustering-model-deploy-tutorial.md)você vai aprender a criar um procedimento armazenado que pode executar clustering em R com base em novos dados.

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

* [Base de Dados Azure SQL com Serviços de Aprendizagem automática (com R)](machine-learning-services-overview.md) ativado.

* Pacote RevoScaleR - Consulte [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) para obter opções para instalar este pacote localmente.

* R IDE - Este tutorial utiliza [o RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Ferramenta de consulta SQL - Este tutorial pressupõe que está a utilizar [o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importar a base de dados de amostras

O conjunto de dados da amostra utilizado neste tutorial foi guardado num ficheiro de backup da base de **dados .bacpac** para que possa descarregar e utilizar. Este conjunto de dados é derivado do conjunto de dados [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp) fornecido pelo [Transaction Processing Performance Council (TPC)](http://www.tpc.org/default.asp).

1. Descarregue o ficheiro [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Siga as instruções em [Import um ficheiro BACPAC para uma base de dados na Base de Dados Azure SQL ou na Azure SQL Gerenciada Instância,](../../azure-sql/database/database-import.md)utilizando estes detalhes:

   * Importe do ficheiro **tpcxbb_1gb.bacpac** que descarregou
   * Durante a pré-visualização pública, escolha a configuração **Gen5/vCore** para a nova base de dados
   * Nomeie a nova base de dados "tpcxbb_1gb"

## <a name="separate-customers"></a>Clientes separados

Crie um novo ficheiro RScript no RStudio e execute o seguinte script.
Na consulta SQL, está a separar os clientes ao longo das seguintes dimensões:

* **orderRatio** = rácio de ordem de devolução (número total de encomendas devolvidas parcial ou integralmente contra o número total de encomendas)
* **itemsRatio** = rácio de artigo de devolução (número total de itens devolvidos em relação ao número de artigos adquiridos)
* **monetaryRatio** = rácio de montante de retorno (montante monetário total de itens devolvidos em relação ao valor adquirido)
* **frequência** = frequência de retorno

Na função **pasta, substitua** **o Servidor,** **UID**e **PWD** com as suas próprias informações de ligação.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Logical SQL server>",
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

## <a name="load-the-data-into-a-data-frame"></a>Carregue os dados num quadro de dados

Agora utilize o seguinte script para devolver os resultados da consulta a uma estrutura de dados R utilizando a função **rxSqlServerData.**
Como parte do processo, definirá o tipo para as colunas selecionadas (utilizando colClasses) para se certificar de que os tipos são corretamente transferidos para R.

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

Deve ver resultados semelhantes aos seguintes.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Limpar recursos

***Se não continuar com este tutorial,*** elimine a base de dados tpcxbb_1gb do seu servidor.

A partir do portal Azure, siga estes passos:

1. A partir do menu à esquerda no portal Azure, selecione Todas as **bases de dados**de **recursos** ou SQL .
1. No **campo Filter by name...** insira **tpcxbb_1gb**e selecione a sua subscrição.
1. Selecione a sua base **de dados tpcxbb_1gb.**
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Na primeira parte desta série tutorial, completou estes passos:

* Importar uma base de dados de amostras numa base de dados na Base de Dados Azure SQL
* Clientes separados ao longo de diferentes dimensões usando R
* Carregue os dados da base de dados num quadro de dados R

Para criar um modelo de machine learning que utilize estes dados do cliente, siga a segunda parte desta série tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Criar um modelo preditivo em R com Azure SQL Database Machine Learning Services (pré-visualização)](clustering-model-build-tutorial.md)
