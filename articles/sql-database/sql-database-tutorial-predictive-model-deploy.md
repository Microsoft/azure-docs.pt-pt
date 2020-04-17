---
title: 'Tutorial: Implementar um modelo preditivo em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na terceira parte deste tutorial em três partes, irá implementar um modelo preditivo em R com serviços de machine learning de base de dados Azure SQL (pré-visualização).
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
ms.openlocfilehash: efcb73866c83dcc03b5db8b7b97f438fb3010511
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452813"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Implemente um modelo preditivo em R com Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)

Na terceira parte deste tutorial em três partes, irá implantar um modelo preditivo, desenvolvido em R, numa base de dados SQL utilizando os Serviços de Machine Learning de Máquinas de Base de Dados Azure SQL (pré-visualização).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Você vai criar um procedimento armazenado com um script R incorporado que faz previsões usando o modelo. Como o seu modelo executa na base de dados Azure SQL, pode ser facilmente treinado contra dados armazenados na base de dados.

Neste artigo, usando os scripts R que desenvolveu nas partes um e dois, aprenderá a:

> [!div class="checklist"]
> * Criar um procedimento armazenado que gere o modelo de aprendizagem automática
> * Guarde o modelo numa tabela de bases de dados
> * Criar um procedimento armazenado que faça previsões usando o modelo
> * Execute o modelo com novos dados

Na [primeira parte,](sql-database-tutorial-predictive-model-prepare-data.md)aprendeu a importar uma base de dados de amostras e, em seguida, preparar os dados para serem utilizados para treinar um modelo preditivo em R.

Na [segunda parte,](sql-database-tutorial-predictive-model-build-compare.md)aprendeu a criar e treinar vários modelos de aprendizagem automática em R, e depois escolher o mais preciso.

## <a name="prerequisites"></a>Pré-requisitos

* A terceira parte desta série tutorial pressupõe que tenha completado a [**parte um**](sql-database-tutorial-predictive-model-prepare-data.md) e a [**segunda parte.**](sql-database-tutorial-predictive-model-build-compare.md)

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Criar um procedimento armazenado que gere o modelo

Na segunda parte desta série tutorial, decidiu que um modelo de árvore de decisão (dtree) era o mais preciso. Agora, usando os scripts R que desenvolveu,`generate_rental_rx_model`crie um procedimento armazenado ( ) que treine e gere o modelo dtree usando o rxDTree a partir do pacote RevoScaleR.

Executar os seguintes comandos no Azure Data Studio ou SSMS.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>Guarde o modelo numa tabela de bases de dados

Crie uma tabela na base de dados tutorialDB e, em seguida, guarde o modelo para a mesa.

1. Crie uma`rental_rx_models`tabela () para armazenar o modelo.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. Guarde o modelo para a mesa como um objeto binário, com o nome do modelo "rxDTree".

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Criar um procedimento armazenado que faça previsões

Crie um procedimento`predict_rentalcount_new`armazenado que faça previsões utilizando o modelo treinado e um conjunto de novos dados.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>Execute o modelo com novos dados

Agora pode usar o `predict_rentalcount_new` procedimento armazenado para prever a contagem de aluguer a partir de novos dados.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

Deve ver um resultado semelhante ao seguinte.

```results
RentalCount_Predicted
332.571428571429
```

Criou, treinou e implementou com sucesso um modelo numa base de dados Azure SQL. Em seguida, usou esse modelo num procedimento armazenado para prever valores com base em novos dados.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de utilizar a base de dados TutorialDB, elimine-a do seu servidor de base de dados Azure SQL.

A partir do portal Azure, siga estes passos:

1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** ou bases de dados **SQL**.
1. No **campo Filter por nome...** introduza **tutorialDB,** e selecione a sua subscrição.
1. Selecione a sua base de dados TutorialDB.
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Na terceira parte desta série tutorial, completou estes passos:

* Criar um procedimento armazenado que gere o modelo de aprendizagem automática
* Guarde o modelo numa tabela de bases de dados
* Criar um procedimento armazenado que faça previsões usando o modelo
* Execute o modelo com novos dados

Para saber mais sobre a utilização de R em Azure SQL Database Machine Learning Services (pré-visualização), consulte:

* [Escreva funções R avançadas na Base de Dados Azure SQL utilizando serviços de aprendizagem automática (pré-visualização)](sql-database-machine-learning-services-functions.md)
* [Trabalhar com dados R e SQL nos Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)](sql-database-machine-learning-services-data-issues.md)
* [Adicione um pacote R aos Serviços de Machine Learning de Máquinas de Base de Dados Azure SQL (pré-visualização)](sql-database-machine-learning-services-add-r-packages.md)
