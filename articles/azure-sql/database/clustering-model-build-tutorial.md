---
title: 'Tutorial: Construir um modelo de agrupamento em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na segunda parte desta série tutorial em três partes, você vai construir um modelo K-Means para executar clustering em R com Azure SQL Database Machine Learning Services (pré-visualização).
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
ms.openlocfilehash: 115688eeccab3a48bf4234f00124bcd6bc950853
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85251442"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Construir um modelo de agrupamento em R com Azure SQL Database Machine Learning Services (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Na segunda parte desta série tutorial em três partes, você vai construir um modelo K-Means em R para executar clustering. Na próxima parte desta série, irá implementar este modelo numa base de dados na Base de Dados Azure SQL com Azure SQL Database Machine Learning Services (pré-visualização).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Neste artigo, aprenderá a:

> [!div class="checklist"]
>
> * Defina o número de clusters para um algoritmo K-Means
> * Realizar agrupamento
> * Analisar os resultados

Na [primeira parte,](clustering-model-prepare-data-tutorial.md)aprendeu a preparar os dados a partir de uma base de dados para realizar o agrupamento.

Na [terceira parte,](clustering-model-deploy-tutorial.md)você vai aprender a criar um procedimento armazenado que pode executar clustering em R com base em novos dados.

## <a name="prerequisites"></a>Pré-requisitos

* A segunda parte deste tutorial pressupõe que completou [**a primeira parte**](clustering-model-prepare-data-tutorial.md) e os seus pré-requisitos.

## <a name="define-the-number-of-clusters"></a>Definir o número de aglomerados

Para agrupar os dados do seu cliente, utilizará o algoritmo de agrupamento **K-Means,** uma das formas mais simples e conhecidas de agrupar dados.
Você pode ler mais sobre K-Means em [Um guia completo para k-significa algoritmo de clustering](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

O algoritmo aceita duas entradas: os dados em si, e um número predefinido "*k*" representando o número de clusters a gerar.
A saída é de aglomerados *k* com os dados de entrada divididos entre os clusters.

Para determinar o número de clusters para o algoritmo usar, use um enredo do grupo dentro da soma de quadrados, pelo número de aglomerados extraídos. O número adequado de aglomerados a utilizar está na curva ou no "cotovelo" do enredo.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted.
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Gráfico de cotovelo](./media/clustering-model-build-tutorial/elbow-graph.png)

Com base no gráfico, parece que *k = 4* seria um bom valor para experimentar. Esse valor *k* irá agrupar os clientes em quatro clusters.

## <a name="perform-clustering"></a>Realizar agrupamento

No seguinte script R, utilizará a função **rxKmeans,** que é a função K-Means no pacote RevoScaleR.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in a database in SQL Database
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

Agora que fez o agrupamento usando k-Means, o próximo passo é analisar o resultado e ver se consegue encontrar alguma informação accível.

O objeto **do clust** contém os resultados do agrupamento K-Means.

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

Os quatro meios de cluster são dados utilizando as variáveis definidas na [primeira parte:](clustering-model-prepare-data-tutorial.md#separate-customers)

* *orderRatio* = rácio de ordem de devolução (número total de encomendas devolvidas parcial ou integralmente contra o número total de encomendas)
* *itemsRatio* = rácio de artigo de devolução (número total de itens devolvidos em relação ao número de artigos adquiridos)
* *monetaryRatio* = rácio de montante de retorno (montante monetário total de itens devolvidos em relação ao valor adquirido)
* *frequência* = frequência de retorno

A mineração de dados utilizando o K-Means requer frequentemente uma análise mais aprofundada dos resultados, e mais passos para entender melhor cada cluster, mas pode fornecer alguns bons leads.
Aqui estão algumas maneiras de interpretar estes resultados:

* O Cluster 1 (o maior cluster) parece ser um grupo de clientes que não estão ativos (todos os valores são zero).
* O Cluster 3 parece ser um grupo que se destaca em termos de comportamento de retorno.

## <a name="clean-up-resources"></a>Limpar recursos

***Se não continuar com este tutorial,*** elimine a base de dados tpcxbb_1gb do seu servidor.

A partir do portal Azure, siga estes passos:

1. A partir do menu à esquerda no portal Azure, selecione Todas as **bases de dados**de **recursos** ou SQL .
1. No **campo Filter by name...** insira **tpcxbb_1gb**e selecione a sua subscrição.
1. Selecione a sua base **de dados tpcxbb_1gb.**
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Na segunda parte desta série tutorial, completou estes passos:

* Defina o número de clusters para um algoritmo K-Means
* Realizar agrupamento
* Analisar os resultados

Para implementar o modelo de aprendizagem automática que criou, siga a terceira parte desta série tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Implementar um modelo de clustering em R com Azure SQL Database Machine Learning Services (pré-visualização)](clustering-model-deploy-tutorial.md)
