---
title: 'Tutorial: Criar um modelo de clustering em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na parte dois desta série de tutoriais de três partes, você criará um modelo K-means para executar o clustering em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia).
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
ms.openlocfilehash: 9f16ebc5acff7bbccc9de28e2fab0d223c6e244b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640008"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Criar um modelo de clustering em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia)

Na parte dois desta série de tutoriais de três partes, você criará um modelo K-means em R para executar o clustering. Na próxima parte desta série, você implantará esse modelo em um banco de dados SQL com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia).

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Definir o número de clusters para um algoritmo K-means
> * Executar clustering
> * Analisar os resultados

Na [parte um](sql-database-tutorial-clustering-model-prepare-data.md), você aprendeu como preparar os dados de um banco de dado SQL do Azure para executar o clustering.

Na [terceira parte](sql-database-tutorial-clustering-model-deploy.md), você aprenderá a criar um procedimento armazenado em um banco de dados SQL do Azure que pode executar o clustering em R com base em novas informações.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* A parte dois deste tutorial pressupõe que você concluiu a [**parte um**](sql-database-tutorial-clustering-model-prepare-data.md) e seus pré-requisitos.

## <a name="define-the-number-of-clusters"></a>Definir o número de clusters

Para clusterizar os dados do cliente, você usará o algoritmo de clustering **K-** Means, uma das maneiras mais simples e mais conhecidas de agrupar dados.
Você pode ler mais sobre K-means em [um guia completo para o algoritmo de clustering k-](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html)means.

O algoritmo aceita duas entradas: Os dados em si e um número predefinido "*k*" que representa o número de clusters a serem gerados.
A saída é *k* clusters com os dados de entrada particionados entre os clusters.

Para determinar o número de clusters para o algoritmo a ser usado, use uma plotagem da soma dos quadrados nos grupos, por número de clusters extraídos. O número apropriado de clusters a ser usado está na curva ou "cotovelo" do gráfico.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Gráfico de cotovelo](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Com base no grafo, parece que *k = 4* seria um bom valor para tentar. Esse valor *k* agrupará os clientes em quatro clusters.

## <a name="perform-clustering"></a>Executar clustering

No script R a seguir, você usará a função **rxKmeans**, que é a função K-means no pacote RevoScaleR.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Analisar os resultados

Agora que você fez o Clustering usando K-means, a próxima etapa é analisar o resultado e ver se você pode encontrar qualquer informação acionável.

O objeto **clust** contém os resultados do clustering K-means.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

Os quatro meios de cluster são fornecidos usando as variáveis definidas na [parte um](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = taxa de ordem de retorno (número total de pedidos parcialmente ou totalmente retornados versus o número total de pedidos)
* *itemsRatio* = taxa de itens de retorno (número total de itens retornados versus o número de itens comprados)
* *monetaryRatio* = taxa de valor de retorno (valor monetário total de itens retornados versus o valor comprado)
* *frequência* = frequência de retorno

A mineração de dados usando K-means geralmente requer análise adicional dos resultados e etapas adicionais para entender melhor cada cluster, mas pode fornecer bons clientes potenciais.
Aqui estão algumas maneiras de interpretar esses resultados:

* O cluster 1 (o maior cluster) parece ser um grupo de clientes que não estão ativos (todos os valores são zero).
* O cluster 3 parece ser um grupo que se destaca em termos de comportamento de retorno.

## <a name="clean-up-resources"></a>Limpar recursos

***Se você não continuar com este tutorial***, exclua o banco de dados tpcxbb_1gb do seu servidor de banco de dados SQL do Azure.

No portal do Azure, siga estas etapas:

1. No menu à esquerda na portal do Azure, selecione **todos os recursos** ou bancos de **dados SQL**.
1. No campo **Filtrar por nome...** , insira **tpcxbb_1gb**e selecione sua assinatura.
1. Selecione o banco de dados **tpcxbb_1gb** .
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Na parte dois desta série de tutoriais, você concluiu estas etapas:

* Definir o número de clusters para um algoritmo K-means
* Executar clustering
* Analisar os resultados

Para implantar o modelo de aprendizado de máquina que você criou, siga a parte três desta série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Implantar um modelo de clustering em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia)](sql-database-tutorial-clustering-model-deploy.md)