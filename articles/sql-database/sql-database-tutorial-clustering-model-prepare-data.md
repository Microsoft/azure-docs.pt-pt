---
title: 'Tutorial: Preparar dados para executar clustering em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na parte um desta série de tutoriais de três partes, você preparará os dados de um banco de dado SQL do Azure para executar o clustering em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia).
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
ms.openlocfilehash: 800dbfc05c47a949bf024e9a5c671979b49ad201
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639983"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Preparar dados para executar clustering em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia)

Na parte um desta série de tutoriais de três partes, você importará e preparará os dados de um banco de dados SQL do Azure usando o R. Mais adiante nesta série, você usará esses dados para treinar e implantar um modelo de clustering em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia).

O clustering pode ser explicado como organizar dados em grupos em que os membros de um grupo são semelhantes de alguma forma.
Você usará o algoritmo **K-** means para executar o clustering de clientes em um conjunto de uma série de compras e Devoluções de produtos. Ao agrupar clientes, você pode concentrar seus esforços de marketing com mais eficiência, direcionando grupos específicos.
O clustering K-means é um algoritmo de *aprendizado* não supervisionado que procura padrões em dados com base em semelhanças.

Nas partes um e dois desta série, você desenvolverá alguns scripts do R no RStudio para preparar seus dados e treinar um modelo de aprendizado de máquina. Em seguida, na parte três, você executará esses scripts R dentro de um banco de dados SQL usando procedimentos armazenados.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Importar um banco de dados de exemplo para um banco de dados SQL do Azure
> * Separar clientes ao longo de dimensões diferentes usando o R
> * Carregar os dados do banco de dados SQL do Azure em um quadro de data do R

Na [parte dois](sql-database-tutorial-clustering-model-build.md), você aprenderá a criar e treinar um modelo de clustering K-means em R.

Na [terceira parte](sql-database-tutorial-clustering-model-deploy.md), você aprenderá a criar um procedimento armazenado em um banco de dados SQL do Azure que pode executar o clustering em R com base em novas informações.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure-se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

* Servidor de banco de dados SQL do Azure com Serviços de Machine Learning habilitado-durante a visualização pública, a Microsoft integrará você e habilitará o aprendizado de máquina para seus bancos de dados novos ou existentes. Siga as etapas em [inscrever-se para a versão prévia](sql-database-machine-learning-services-overview.md#signup).

* Pacote RevoScaleR – consulte [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) para obter opções para instalar esse pacote localmente.

* R IDE-este tutorial usa o [RStudio desktop](https://www.rstudio.com/products/rstudio/download/).

* Ferramenta de consulta SQL – este tutorial pressupõe que você esteja usando [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importar o banco de dados de exemplo

O conjunto de dados de exemplo usado neste tutorial foi salvo em um arquivo de backup do banco de dados **. bacpac** para você baixar e usar. Esse conjunto de DataSet é derivado do conjunto de [tpcx-BB](http://www.tpc.org/tpcx-bb/default.asp) fornecido pelo [TPC (Conselho de desempenho de processamento de transações)](http://www.tpc.org/default.asp).

1. Baixe o arquivo [tpcxbb_1gb. bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Siga as instruções em [importar um arquivo BACPAC para criar um banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-import)usando estes detalhes:

   * Importar do arquivo **tpcxbb_1gb. bacpac** que você baixou
   * Durante a visualização pública, escolha a configuração **Gen5/vCore** para o novo banco de dados
   * Nomeie o novo banco de dados "tpcxbb_1gb"

## <a name="separate-customers"></a>Clientes separados

Crie um novo arquivo RScript no RStudio e execute o script a seguir.
Na consulta SQL, você está separando clientes ao longo das seguintes dimensões:

* **orderRatio** = taxa de ordem de retorno (número total de pedidos parcialmente ou totalmente retornados versus o número total de pedidos)
* **itemsRatio** = taxa de itens de retorno (número total de itens retornados versus o número de itens comprados)
* **monetaryRatio** = taxa de valor de retorno (valor monetário total de itens retornados versus o valor comprado)
* **frequência** = frequência de retorno

Na função **Paste** , substitua **Server**, **UID**e **pwd** pelas suas próprias informações de conexão.

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

## <a name="load-the-data-into-a-data-frame"></a>Carregar os dados em um quadro de dados

Agora, use o script a seguir para retornar os resultados da consulta para um quadro de dados do R usando a função **rxSqlServerData** .
Como parte do processo, você definirá o tipo das colunas selecionadas (usando colClasses) para garantir que os tipos sejam transferidos corretamente para o R.

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

Você deverá ver resultados semelhantes ao seguinte.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Limpar recursos

***Se você não continuar com este tutorial***, exclua o banco de dados tpcxbb_1gb do seu servidor de banco de dados SQL do Azure.

No portal do Azure, siga estas etapas:

1. No menu à esquerda na portal do Azure, selecione **todos os recursos** ou bancos de **dados SQL**.
1. No campo **Filtrar por nome...** , insira **tpcxbb_1gb**e selecione sua assinatura.
1. Selecione o banco de dados **tpcxbb_1gb** .
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Na parte um desta série de tutoriais, você concluiu estas etapas:

* Importar um banco de dados de exemplo para um banco de dados SQL do Azure
* Separar clientes ao longo de dimensões diferentes usando o R
* Carregar os dados do banco de dados SQL do Azure em um quadro de data do R

Para criar um modelo de aprendizado de máquina que usa esses dados do cliente, siga a parte dois desta série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Criar um modelo de previsão em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia)](sql-database-tutorial-clustering-model-build.md)
