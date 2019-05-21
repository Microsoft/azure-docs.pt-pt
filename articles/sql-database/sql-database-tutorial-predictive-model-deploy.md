---
title: 'Tutorial: Implementar um modelo preditivo em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na terceira parte deste tutorial de três partes, vai implementar um modelo preditivo em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização).
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
ms.openlocfilehash: 17b68f71f4034e5eb637d40b975cc22d94438fb7
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978692"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Implementar um modelo preditivo em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)

Na terceira parte deste tutorial de três partes, vai implementar um modelo preditivo em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização).

Irá criar um procedimento armazenado com um script R incorporado que faz predições com o modelo. Como seu modelo é executado na base de dados SQL do Azure, pode facilmente ser treinado em relação a dados armazenados na base de dados.

Neste artigo, ficará a saber como:

> [!div class="checklist"]
> * Store modelo preditivo numa tabela de base de dados
> * Criar um procedimento armazenado que gera o modelo
> * Criar um procedimento armazenado que faz predições com o modelo
> * Executar o modelo com dados novos

Na [parte um](sql-database-tutorial-predictive-model-prepare-data.md), ficou a saber como importar uma base de dados de exemplo para uma base de dados SQL do Azure e, em seguida, prepare os dados a ser utilizado para preparar um modelo de previsão em R.

Na [parte dois](sql-database-tutorial-predictive-model-build-compare.md), aprendeu a criar e utilizar vários modelos e, em seguida, escolher a mais precisos.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Terceira parte desta série de tutoriais pressupõe que concluiu [ **parte um** ](sql-database-tutorial-predictive-model-prepare-data.md) e [ **parte dois**](sql-database-tutorial-predictive-model-build-compare.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Criar um procedimento armazenado que gera o modelo

Na segunda parte desta série de tutoriais, decidiu que um modelo de árvore (dtree) de decisão era mais precisos. Agora, crie um procedimento armazenado (`generate_rental_rx_model`) que prepara e gera o modelo de dtree usando rxDTree do pacote de RevoScaleR.

Execute os seguintes comandos no Studio de dados do Azure ou no SSMS.

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

## <a name="store-the-model-in-a-database-table"></a>O modelo de uma tabela de base de dados do Store

Criar uma tabela na base de dados TutorialDB e, em seguida, guarde o modelo para a tabela.

1. Criar uma tabela (`rental_rx_models`) para armazenar o modelo.

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

1. Guarde o modelo de tabela como um objeto binário, com o nome de modelo "rxDTree".

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

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Criar um procedimento armazenado que faz predições

Criar um procedimento armazenado (`predict_rentalcount_new`) que faz predições com o modelo preparado e um conjunto de novos dados.

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

## <a name="execute-the-model-with-new-data"></a>Executar o modelo com dados novos

Agora, pode utilizar o procedimento armazenado `predict_rentalcount_new` para prever a contagem de aluguer de novos dados.

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

Deverá ver um resultado semelhante ao seguinte.

```results
RentalCount_Predicted
332.571428571429
```

Que com êxito criado, formação e implementar um modelo numa base de dados SQL do Azure. Utilizado, em seguida, esse modelo num procedimento armazenado para prever valores com base nos novos dados.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de utilizar a base de dados TutorialDB, eliminá-lo a partir do seu servidor de base de dados do Azure SQL.

No portal do Azure, siga estes passos:

1. No menu esquerdo no portal do Azure, selecione **todos os recursos** ou **bases de dados SQL**.
1. Na **filtrar por nome...**  , insira **TutorialDB**e selecione a sua subscrição.
1. Selecione a sua base de dados TutorialDB.
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Na terceira parte desta série de tutoriais, concluiu estes passos:

* Store modelo preditivo numa tabela de base de dados
* Criar um procedimento armazenado que gera o modelo
* Criar um procedimento armazenado que faz predições com o modelo
* Executar o modelo com dados novos

Para saber mais sobre como utilizar o R no Machine Learning Services (pré-visualização) do banco de dados do Azure SQL, veja:

* [Escrever funções avançadas do R na base de dados do SQL Azure com serviços do Machine Learning (pré-visualização)](sql-database-machine-learning-services-functions.md)
* [Trabalhar com dados de R e SQL no SQL da base de dados serviços Azure Machine Learning (pré-visualização)](sql-database-machine-learning-services-data-issues.md)
* [Adicionar um pacote de R para serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)](sql-database-machine-learning-services-add-r-packages.md)
