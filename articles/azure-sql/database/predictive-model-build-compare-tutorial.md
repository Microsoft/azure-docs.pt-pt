---
title: 'Tutorial: Treine e compare modelos preditivos em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na segunda parte desta série tutorial em três partes, irá criar dois modelos preditivos em R com Azure SQL Database Machine Learning Services (pré-visualização) e, em seguida, selecione o modelo mais preciso.
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
ms.date: 07/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ca0af9a34587f8d3a3c0502c77556975b1d8df4e
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253839"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Criar um modelo preditivo em R com Azure SQL Database Machine Learning Services (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Na segunda parte desta série tutorial em três partes, você vai criar dois modelos preditivos em R e selecionar o modelo mais preciso. Na próxima parte desta série, irá implementar este modelo numa base de dados na Base de Dados Azure SQL com Azure SQL Database Machine Learning Services (pré-visualização).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Neste artigo, aprenderá a:

> [!div class="checklist"]
>
> * Treine dois modelos de aprendizagem automática
> * Fazer previsões de ambos os modelos
> * Compare os resultados para escolher o modelo mais preciso

Na [primeira parte,](predictive-model-prepare-data-tutorial.md)aprendeu a importar uma base de dados de amostras e, em seguida, a preparar os dados para serem utilizados para a formação de um modelo preditivo em R.

Na [terceira parte,](predictive-model-deploy-tutorial.md)aprenderá a armazenar o modelo numa base de dados e, em seguida, criará procedimentos armazenados a partir dos scripts R que desenvolveu nas partes um e dois. Os procedimentos armazenados serão executados numa base de dados para fazer previsões com base em novos dados.

## <a name="prerequisites"></a>Pré-requisitos

* A segunda parte deste tutorial pressupõe que completou [**a primeira parte**](predictive-model-prepare-data-tutorial.md) e os seus pré-requisitos.

## <a name="train-two-models"></a>Treine dois modelos

Para encontrar o melhor modelo para os dados de aluguer de esqui, crie dois modelos diferentes (regressão linear e árvore de decisão) e veja qual está a prever com mais precisão. Usará o quadro de dados `rentaldata` que criou na primeira parte desta série.

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>Fazer previsões de ambos os modelos

Utilize uma função de previsão para prever as contagens de aluguer usando cada modelo treinado.

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>Comparar os resultados

Agora quer ver qual dos modelos dá as melhores previsões. Uma forma rápida e fácil de o fazer é usar uma função básica de conspiração para ver a diferença entre os valores reais nos seus dados de treino e os valores previstos.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Comparando os dois modelos](./media/predictive-model-build-compare-tutorial/compare-models.png)

Parece que o modelo da árvore de decisão é o mais preciso dos dois modelos.

## <a name="clean-up-resources"></a>Limpar recursos

Se não continuar com este tutorial, elimine a base de dados TutorialDB do seu servidor.

A partir do portal Azure, siga estes passos:

1. A partir do menu à esquerda no portal Azure, selecione Todas as **bases de dados**de **recursos** ou SQL .
1. No **filtro pelo nome...** campo, insira **TutorialDB**e selecione a sua subscrição.
1. Selecione a sua base de dados TutorialDB.
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Na segunda parte desta série tutorial, completou estes passos:

* Treine dois modelos de aprendizagem automática
* Fazer previsões de ambos os modelos
* Compare os resultados para escolher o modelo mais preciso

Para implementar o modelo de aprendizagem automática que criou, siga a terceira parte desta série tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Implementar um modelo preditivo em R com Azure SQL Database Machine Learning Services (pré-visualização)](predictive-model-deploy-tutorial.md)
