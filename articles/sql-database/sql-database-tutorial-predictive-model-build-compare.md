---
title: 'Tutorial: Treinar e comparar modelos de previsão em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na parte dois desta série de tutoriais de três partes, você criará dois modelos de previsão em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia) e, em seguida, selecionará o modelo mais preciso.
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
ms.openlocfilehash: 2c85a378dc219e8af1b6458344ee4dba0fa73e68
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596809"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Criar um modelo de previsão em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia)

Na parte dois desta série de tutoriais de três partes, você criará dois modelos de previsão em R e selecionará o modelo mais preciso. Na próxima parte desta série, você implantará esse modelo em um banco de dados SQL com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia).

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Treinar dois modelos de aprendizado de máquina
> * Fazer previsões de ambos os modelos
> * Compare os resultados para escolher o modelo mais preciso

Na [primeira parte](sql-database-tutorial-predictive-model-prepare-data.md), você aprendeu a importar um banco de dados de exemplo e, em seguida, a preparar o dado a ser usado para treinar um modelo de previsão em R.

Na [terceira parte](sql-database-tutorial-predictive-model-deploy.md), você aprenderá a armazenar o modelo em um banco de dados e, em seguida, criar procedimentos armazenados dos scripts R desenvolvidos nas partes um e dois. Os procedimentos armazenados serão executados em um banco de dados SQL para fazer previsões com base em um novo dado.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* A parte dois deste tutorial pressupõe que você concluiu a [**parte um**](sql-database-tutorial-predictive-model-prepare-data.md) e seus pré-requisitos.

## <a name="train-two-models"></a>Treinar dois modelos

Para encontrar o melhor modelo para os dados de aluguel de esqui, crie dois modelos diferentes (regressão linear e árvore de decisão) e veja qual deles está prevendo com mais precisão. Você usará o quadro `rentaldata` de dados criado na parte um desta série.

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

Use uma função de previsão para prever as contagens de aluguel usando cada modelo treinado.

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

Agora você deseja ver quais dos modelos oferecem as melhores previsões. Uma maneira rápida e fácil de fazer isso é usar uma função básica de plotagem para exibir a diferença entre os valores reais nos dados de treinamento e os valores previstos.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Comparando os dois modelos](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Parece que o modelo de árvore de decisão é mais preciso dos dois modelos.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar com este tutorial, exclua o banco de dados TutorialDB do seu servidor de banco de dados SQL do Azure.

No portal do Azure, siga estas etapas:

1. No menu à esquerda na portal do Azure, selecione **todos os recursos** ou bancos de **dados SQL**.
1. No campo **Filtrar por nome...** , insira **TutorialDB**e selecione sua assinatura.
1. Selecione o banco de dados TutorialDB.
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Na parte dois desta série de tutoriais, você concluiu estas etapas:

* Treinar dois modelos de aprendizado de máquina
* Fazer previsões de ambos os modelos
* Compare os resultados para escolher o modelo mais preciso

Para implantar o modelo de aprendizado de máquina que você criou, siga a parte três desta série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Implantar um modelo de previsão em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia)](sql-database-tutorial-predictive-model-deploy.md)
