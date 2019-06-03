---
title: 'Tutorial: Criar um modelo de clustering em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na segunda parte desta série de tutoriais de três partes, irá criar um modelo de K-Means para executar o clustering no R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização).
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
ms.openlocfilehash: 12738b63be92420c5f3afea6c133522cbd97f849
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419763"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Criar um modelo de clustering em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)

Na segunda parte desta série de tutoriais de três partes, irá criar um modelo de K-Means para executar o clustering no R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização).

Neste artigo, ficará a saber como:

> [!div class="checklist"]
> * Definir o número de clusters para um algoritmo K-Means
> * Executar o clustering
> * Analisar os resultados

Na [parte um](sql-database-tutorial-clustering-model-prepare-data.md), ficou a saber como preparar os dados de uma base de dados SQL do Azure para executar o clustering em R.

Na [parte três](sql-database-tutorial-clustering-model-deploy.md), ficará a saber como criar um procedimento armazenado numa base de dados SQL do Azure que pode executar o clustering com base nos dados de novo.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* A parte dois deste tutorial pressupõe que concluiu [ **parte um** ](sql-database-tutorial-clustering-model-prepare-data.md) e seus pré-requisitos.

## <a name="define-the-number-of-clusters"></a>Definir o número de clusters

Colocar em cluster os dados dos clientes, irá utilizar o **K-Means** algoritmo de clustering, uma das formas mais simples e mais conhecidas de dados de agrupamento.
Pode ler mais sobre K-Means na [um guia completo para o algoritmo de clustering de K-means](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

O algoritmo aceita duas entradas: Os dados propriamente ditos e um número predefinido "*k*" que representa o número de clusters para gerar.
O resultado é *k* clusters com os dados de entrada particionados entre os clusters.

Para determinar o número de clusters para o algoritmo a utilizar, utilize um desenho do dentro de soma de grupos de quadrados, pelo número de clusters extraídos. É o número apropriado de clusters a serem usados no reposicione ou "elbow" do desenho.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Gráfico de elbow](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Com base no gráfico, parece que *k = 4* seria um bom valor para experimentar. Que *k* valor agrupará os clientes em quatro clusters.

## <a name="perform-clustering"></a>Executar o clustering

O seguinte script do R, irá utilizar a função **rxKmeans**, que é a função de K-Means no pacote de RevoScaleR.

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

Agora que tiver feito o clustering usando K-Means, a próxima etapa é analisar o resultado e veja se pode encontrar todas as informações acionáveis.

O **clust** objeto contém os resultados do clustering de K-Means.

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

Os meios de cluster de quatro fornecido com as variáveis definidos no [parte um](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = rácio de ordem de retorno (o número total de pedidos total ou parcialmente devolvido em comparação com o número total de pedidos)
* *itemsRatio* = rácio de item de retorno (o número total de itens retornados em comparação com o número de itens comprados)
* *monetaryRatio* = rácio de valor de retorno (montante monetário total de itens retornados em comparação com a quantidade comprada)
* *frequência* = frequência de retorna

Extração de dados usando K-Means, muitas vezes, ainda mais requer a análise dos resultados e ainda mais os passos para compreender melhor a cada cluster, mas ele pode fornecer algumas oportunidades potenciais boa.
Seguem-se duas maneiras que pode interpretar esses resultados:

* Cluster de 1 (no cluster maior) parece ser um grupo de clientes que não estão ativas (todos os valores são zero).
* Cluster 3 parece ser um grupo que se destaca em termos de comportamento de retorno.

## <a name="clean-up-resources"></a>Limpar recursos

***Se não pretender continuar com este tutorial***, eliminar a base de dados tpcxbb_1gb do seu servidor de base de dados do Azure SQL.

No portal do Azure, siga estes passos:

1. No menu esquerdo no portal do Azure, selecione **todos os recursos** ou **bases de dados SQL**.
1. Na **filtrar por nome...**  , insira **tpcxbb_1gb**e selecione a sua subscrição.
1. Selecione seu **tpcxbb_1gb** base de dados.
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Na segunda parte desta série de tutoriais, concluiu estes passos:

* Definir o número de clusters para um algoritmo K-Means
* Executar o clustering
* Analisar os resultados

Para implementar o modelo de machine learning que criou, siga a terceira parte desta série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Implementar um modelo de clustering em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)](sql-database-tutorial-clustering-model-deploy.md)