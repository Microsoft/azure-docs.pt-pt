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
ms.openlocfilehash: a7d0bc787f1ae4444849a1483d6323f8a098ae1e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053412"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Construa um modelo de clustering em R com Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Na segunda parte desta série tutorial em três partes, você vai construir um modelo K-Means em R para executar clustering. Na próxima parte desta série, irá supor este modelo numa base de dados SQL com serviços de machine learning de base de dados Azure SQL (pré-visualização).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Neste artigo, aprenderá a:

> [!div class="checklist"]
>
> * Defina o número de clusters para um algoritmo K-Means
> * Realizar agrupamentos
> * Analisar os resultados

Na [primeira parte,](clustering-model-prepare-data-tutorial.md)aprendeu a preparar os dados a partir de uma base de dados Azure SQL para realizar o agrupamento.

Na [terceira parte,](clustering-model-deploy-tutorial.md)aprenderá a criar um procedimento armazenado numa base de dados Azure SQL que pode realizar clustering em R com base em novos dados.

## <a name="prerequisites"></a>Pré-requisitos

* A segunda parte deste tutorial assume que completou a [**primeira parte**](clustering-model-prepare-data-tutorial.md) e os seus pré-requisitos.

## <a name="define-the-number-of-clusters"></a>Definir o número de clusters

Para agrupar os dados dos seus clientes, utilizará o algoritmo de agrupamento **K-Means,** uma das formas mais simples e conhecidas de agrupar dados.
Pode ler mais sobre K-Means em [Um guia completo para algoritmo de clustering K-means](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

O algoritmo aceita duas inputs: os próprios dados, e um número predefinido "*k*" representando o número de clusters a gerar.
A saída é *k* clusters com os dados de entrada divididos entre os clusters.

Para determinar o número de clusters para o algoritmo utilizar, use um enredo da soma de grupos dentro de quadrados, em número de clusters extraídos. O número adequado de aglomerados a utilizar está na curva ou no "cotovelo" do enredo.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted.
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Gráfico de cotovelo](./media/clustering-model-build-tutorial/elbow-graph.png)

Com base no gráfico, parece que *k = 4* seria um bom valor para tentar. Esse valor *k* irá agrupar os clientes em quatro clusters.

## <a name="perform-clustering"></a>Realizar agrupamentos

No seguinte script R, utilizará a função **rxKmeans**, que é a função K-Means no pacote RevoScaleR.

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

Agora que fez o agrupamento usando K-Means, o próximo passo é analisar o resultado e ver se consegue encontrar alguma informação atol.

O objeto de **clust** contém os resultados do agrupamento K-Means.

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

Os quatro meios de agrupamento são dadas utilizando as variáveis definidas na [primeira parte:](clustering-model-prepare-data-tutorial.md#separate-customers)

* *encomendaRatio* = rácio de encomenda de devolução (número total de encomendas parcialmente ou totalmente devolvidas contra o número total de encomendas)
* *rubricasRatio* = relação de ida e volta (número total de itens devolvidos contra o número de itens adquiridos)
* *valor monetário* = rácio de montante de retorno (montante monetário total dos itens devolvidos contra o montante adquirido)
* *frequência* = frequência de retorno

A mineração de dados utilizando K-Means requer frequentemente uma análise mais aprofundada dos resultados, e outros passos para entender melhor cada cluster, mas pode fornecer algumas boas pistas.
Aqui estão algumas maneiras de interpretar estes resultados:

* O Cluster 1 (o maior cluster) parece ser um grupo de clientes que não estão ativos (todos os valores são zero).
* O Cluster 3 parece ser um grupo que se destaca em termos de comportamento de retorno.

## <a name="clean-up-resources"></a>Limpar recursos

***Se não vai continuar com este tutorial,*** elimine a base de dados tpcxbb_1gb do seu servidor.

A partir do portal Azure, siga estes passos:

1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** ou bases de dados **SQL**.
1. No **campo Filter por nome...** introduza **tpcxbb_1gb,** e selecione a sua subscrição.
1. Selecione a sua base de dados **tpcxbb_1gb.**
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

Na segunda parte desta série tutorial, completou estes passos:

* Defina o número de clusters para um algoritmo K-Means
* Realizar agrupamentos
* Analisar os resultados

Para implementar o modelo de aprendizagem automática que criou, siga a terceira parte desta série tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Implementar um modelo de clustering em R com Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)](clustering-model-deploy-tutorial.md)
