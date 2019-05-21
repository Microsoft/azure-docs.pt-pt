---
title: 'Tutorial: Ensinar e comparar os modelos de previsão em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na segunda parte desta série de tutoriais de três partes, irá criar dois modelos de previsão em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização) e, em seguida, selecione o modelo mais exato.
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
ms.date: 05/02/2019
ms.openlocfilehash: 3d336d6a53b6d234048c56d8492d278bef6fed64
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957611"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Criar um modelo preditivo em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)

Na segunda parte desta série de tutoriais de três partes, irá criar dois modelos de previsão em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização) e, em seguida, selecione o modelo mais exato.

Neste artigo, ficará a saber como:

> [!div class="checklist"]
> * Treinar os dois modelos de machine learning
> * Fazer predições de ambos os modelos
> * Compare os resultados para escolher o modelo mais exato

Na [parte um](sql-database-tutorial-predictive-model-prepare-data.md), ficou a saber como importar uma base de dados de exemplo para uma base de dados SQL do Azure e, em seguida, prepare os dados a ser utilizado para preparar um modelo de previsão em R.

Na [parte três](sql-database-tutorial-predictive-model-deploy.md), irá aprender a armazenar o modelo numa base de dados e, em seguida, crie um procedimento armazenado que pode realizar predições com base nos dados de novo.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* A parte dois deste tutorial pressupõe que concluiu [ **parte um** ](sql-database-tutorial-predictive-model-prepare-data.md) e seus pré-requisitos.

## <a name="train-two-models"></a>Criar dois modelos

Para localizar o melhor modelo de dados de aluguer de esqui, criar dois modelos diferentes (regressão linear e árvore de decisão) e veja qual delas é a previsão com mais precisão. Usará o quadro de dados `rentaldata` que criou na primeira parte desta série.

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

## <a name="make-predictions-from-both-models"></a>Fazer predições de ambos os modelos

Utilize uma função predict para predict que o aluguel de conta com cada modelo preparado.

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

## <a name="compare-the-results"></a>Compare os resultados

Agora pretende ver que os modelos fornece as melhores previsões. Uma maneira rápida e fácil de fazer isso é usar uma função de desenho básica para ver a diferença entre os valores reais nos seus dados de treinamento e os valores previstos.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Comparando os dois modelos](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Parece que o modelo de árvore de decisão é mais precisa dos dois modelos.

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar com este tutorial, elimine a base de dados TutorialDB do seu servidor de base de dados do Azure SQL.

No portal do Azure, siga estes passos:

1. No menu esquerdo no portal do Azure, selecione **todos os recursos** ou **bases de dados SQL**.
1. Na **filtrar por nome...**  , insira **TutorialDB**e selecione a sua subscrição.
1. Selecione a sua base de dados TutorialDB.
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Na segunda parte desta série de tutoriais, concluiu estes passos:

* Treinar os dois modelos de machine learning
* Fazer predições de ambos os modelos
* Compare os resultados para escolher o modelo mais exato

Para implementar o modelo de machine learning que criou, siga a terceira parte desta série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Implementar um modelo preditivo em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)](sql-database-tutorial-predictive-model-deploy.md)
