---
title: 'Tutorial: Preparar dados para realizar agrupamentos em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na primeira parte desta série tutorial em três partes, irá preparar os dados de uma base de dados Azure SQL para realizar o agrupamento em R com o Azure SQL Database Machine Learning Services (pré-visualização).
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
ms.openlocfilehash: abe7d5ed1d4ba1308abde04aee32a3ea222456b8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81452881"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Prepare dados para realizar clustering em R com Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)

Na primeira parte desta série tutorial em três partes, importará e preparará os dados de uma base de dados Azure SQL usando R. Mais tarde nesta série, utilizará estes dados para treinar e implementar um modelo de clustering em R com os Serviços de Machine Learning machine learning da Azure SQL (pré-visualização).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

*O agrupamento* pode ser explicado como organização de dados em grupos onde os membros de um grupo são semelhantes de alguma forma.
Utilizará o algoritmo **K-Means** para realizar o agrupamento de clientes num conjunto de dados de compras e devoluções de produtos. Ao agrupar clientes, pode concentrar os seus esforços de marketing de forma mais eficaz, direcionando grupos específicos.
O clustering K-Means é um algoritmo de *aprendizagem não supervisionado* que procura padrões em dados baseados em semelhanças.

Nas partes um e dois desta série, desenvolverá alguns scripts R no RStudio para preparar os seus dados e treinar um modelo de aprendizagem automática. Depois, na terceira parte, executarás os scripts R dentro de uma base de dados SQL usando procedimentos armazenados.

Neste artigo, aprenderá a:

> [!div class="checklist"]
> * Importar uma base de dados de amostras numa base de dados Azure SQL
> * Clientes separados ao longo de diferentes dimensões usando R
> * Carregue os dados da base de dados Azure SQL num quadro de dados R

Na [segunda parte,](sql-database-tutorial-clustering-model-build.md)aprenderás a criar e treinar um modelo de agrupamento K-Means em R.

Na [terceira parte,](sql-database-tutorial-clustering-model-deploy.md)aprenderá a criar um procedimento armazenado numa base de dados Azure SQL que pode realizar clustering em R com base em novos dados.

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - Se não tiver uma subscrição Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

* [Base de dados Azure SQL com Serviços de Aprendizagem Automática (com R)](sql-database-machine-learning-services-overview.md) ativados.

* Pacote RevoScaleR - Consulte [o RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) para obter opções para instalar este pacote localmente.

* R IDE - Este tutorial utiliza [o RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Ferramenta de consulta SQL - Este tutorial assume que está a usar o [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importar a base de dados da amostra

O conjunto de dados da amostra utilizado neste tutorial foi guardado num ficheiro de backup da base de dados **.bacpac** para que possa descarregar e utilizar. Este conjunto de dados é derivado do conjunto de dados [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp) fornecido pelo [Transaction Processing Performance Council (TPC)](http://www.tpc.org/default.asp).

1. Faça o download do ficheiro [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Siga as instruções em Importar um ficheiro BACPAC para criar uma base de [dados Azure SQL,](https://docs.microsoft.com/azure/sql-database/sql-database-import)utilizando estes detalhes:

   * Importa do ficheiro **tpcxbb_1gb.bacpac** que descarregou
   * Durante a pré-visualização pública, escolha a configuração **Gen5/vCore** para a nova base de dados
   * Nomeie a nova base de dados "tpcxbb_1gb"

## <a name="separate-customers"></a>Clientes separados

Crie um novo ficheiro RScript no RStudio e execute o seguinte script.
Na consulta SQL, está a separar os clientes ao longo das seguintes dimensões:

* **encomendaRatio** = rácio de encomenda de devolução (número total de encomendas parcialmente ou totalmente devolvidas contra o número total de encomendas)
* **rubricasRatio** = relação de ida e volta (número total de itens devolvidos contra o número de itens adquiridos)
* **valor monetário** = rácio de montante de retorno (montante monetário total dos itens devolvidos contra o montante adquirido)
* **frequência** = frequência de retorno

Na função **pasta,** substitua o **Server,** **o UID**e o **PWD** com as suas próprias informações de ligação.

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

## <a name="load-the-data-into-a-data-frame"></a>Carregue os dados num quadro de dados

Utilize agora o seguinte script para devolver os resultados da consulta a um quadro de dados R utilizando a função **rxSqlServerData.**
Como parte do processo, definirá o tipo para as colunas selecionadas (utilizando colClasses) para se certificar de que os tipos são transferidos corretamente para R.

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

***Se não vai continuar com este tutorial,*** elimine a base de dados tpcxbb_1gb do seu servidor de base de dados Azure SQL.

A partir do portal Azure, siga estes passos:

1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** ou bases de dados **SQL**.
1. No **campo Filter por nome...** introduza **tpcxbb_1gb,** e selecione a sua subscrição.
1. Selecione a sua base de dados **tpcxbb_1gb.**
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Na primeira parte desta série tutorial, completou estes passos:

* Importar uma base de dados de amostras numa base de dados Azure SQL
* Clientes separados ao longo de diferentes dimensões usando R
* Carregue os dados da base de dados Azure SQL num quadro de dados R

Para criar um modelo de machine learning que utilize estes dados do cliente, siga a segunda parte desta série tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Criar um modelo preditivo em R com Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)](sql-database-tutorial-clustering-model-build.md)
