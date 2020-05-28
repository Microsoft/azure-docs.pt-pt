---
title: Criar e treinar um modelo preditivo em R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Crie um modelo preditivo simples em R utilizando os Serviços de Machine Learning machine learning da Base de Dados Azure SQL (pré-visualização), e depois preveja um resultado usando novos dados.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6dee5d6e1bb2802114d7bba14a57b91dbab09e19
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054792"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Quickstart: Criar e treinar um modelo preditivo em R com serviços de machine learning de base de dados Azure SQL (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste arranque rápido, cria e treina um modelo preditivo utilizando R, guarde o modelo para uma tabela na sua base de dados e, em seguida, use o modelo para prever valores a partir de novos dados usando Serviços de Aprendizagem Automática (com R) na Base de Dados Azure SQL.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma [base de dados em Azure SQL Database](single-database-create-quickstart.md) com uma regra de firewall ao [nível do servidor](firewall-create-server-level-portal-quickstart.md)
- [Serviços de Aprendizagem automática](machine-learning-services-overview.md) com R ativado.
- [Estúdio de Gestão de Servidores SQL](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

Este exemplo usa um modelo simples de regressão para prever a distância de paragem de um carro com base na velocidade utilizando o conjunto de dados dos **carros** incluído com R.

> [!TIP]
> Muitos conjuntos de dados estão incluídos com o tempo de execução r, para obter uma lista de conjuntos de dados instalados, tipo `library(help="datasets")` a partir do pedido de comando R.

## <a name="create-and-train-a-predictive-model"></a>Criar e treinar um modelo preditivo

Os dados da velocidade do carro no conjunto de dados dos **carros** contêm duas colunas, ambas numéricas: **dist** e **velocidade**. Os dados incluem múltiplas observações de paragem a diferentes velocidades. A partir destes dados, você vai criar um modelo linear de regressão que descreve a relação entre a velocidade do carro e a distância necessária para parar um carro.

Os requisitos dos modelos lineares são simples:
- Defina uma fórmula que descreva a relação entre a *velocidade* variável dependente e a *distância*variável independente .
- Forneça os dados de entrada que vão ser utilizados na preparação do modelo.

> [!TIP]
> Se precisar de um atualização em modelos lineares, experimente este tutorial que descreve o processo de montagem de um modelo utilizando rxLinMod: [Modelos Lineares de Montagem](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

Nos seguintes passos, irá configurar os dados de treino, criar um modelo de regressão, treiná-lo usando os dados de treino e, em seguida, guardar o modelo para uma tabela SQL.

1. Abra o **Estúdio de Gestão de Servidores SQL** e ligue-se à sua base de dados SQL.

   Se precisar de ajuda para se ligar, consulte [Quickstart: Use o Estúdio de Gestão de Servidores SQL para ligar e consultar uma base de dados Azure SQL](connect-query-ssms.md).

1. Crie a tabela **CarSpeed** para guardar os dados de treino.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. Criar um modelo de regressão usando `rxLinMod` . 

   Para construir o modelo, define a fórmula dentro do código R e, em seguida, passa os dados de treino **CarSpeed** como parâmetro de entrada.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     O primeiro argumento de rxLinMod é o parâmetro *formula*, que define a distância como estando dependente da velocidade. Os dados de entrada são armazenados na variável `CarsData`, que é preenchida pela consulta SQL.

1. Crie uma tabela onde guarde o modelo para que possa usá-lo mais tarde para previsão. 

   A saída de um pacote R que cria um modelo é geralmente um **objeto binário,** pelo que a tabela deve ter uma coluna do tipo **VARBINARY (máx).**

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Agora ligue para o procedimento armazenado, gere o modelo e guarde-o para uma mesa.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Tenha em conta que, se executar este código uma segunda vez, obterá o erro:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Uma opção para evitar este erro é atualizar o nome para cada novo modelo. Por exemplo, pode mudá-lo para algo mais descritivo e incluir o tipo de modelo, o dia em que o criou, etc.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Ver a tabela de coeficientes

Geralmente, a saída de R do procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) está limitada a um único frame de dados. No entanto, pode devolver saídas de outros tipos, como escalares, para além do frame de dados.

Por exemplo, suponha que queira treinar um modelo mas veja imediatamente a tabela de coeficientes do modelo. Para tal, cria a tabela de coeficientes como o conjunto de resultados principais, e produz o modelo treinado numa variável SQL. Pode reutilizar imediatamente o modelo chamando a variável, ou pode guardar o modelo para uma mesa, como mostrado aqui.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**Resultados**

![Modelo preparado com saída adicional](./media/r-train-score-model-create-quickstart/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Marque novos dados usando o modelo treinado

*Pontuar* é um termo usado na ciência dos dados para significar gerar previsões, probabilidades ou outros valores com base em novos dados alimentados num modelo treinado. Utilizará o modelo que criou na secção anterior para marcar previsões contra novos dados.

Reparou que os dados de preparação originais param nos 45 quilómetros por hora? Isto deve-se ao facto de os dados originais terem por base uma experimentação de 1920! Quanto tempo demoraria um automóvel dos anos 20 a parar se pudesse ir tão rápido como 100 km/h ou mesmo 160 km/h? Para responder a esta pergunta, pode fornecer novos valores de velocidade ao seu modelo.

1. Crie uma tabela com novos dados de velocidade.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. Preveja a distância de paragem destes novos valores de velocidade.

   Como o seu modelo é baseado no algoritmo **rxLinMod** fornecido como parte do pacote **RevoScaleR,** você chama a função [rxPredict,](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) em vez da função R `predict` genérica.

   Este roteiro de exemplo:
   - Usa uma declaração SELECT para obter um único modelo da tabela
   - Passa-o como parâmetro de entrada
   - Chama a `unserialize` função no modelo
   - Aplica a `rxPredict` função com argumentos adequados ao modelo
   - Fornece os novos dados de entrada

   > [!TIP]
   > Para pontuação em tempo real, consulte [as funções](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) de Serialização fornecidas pela RevoScaleR.

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **Resultados**

   ![Conjunto de resultados para prever a distância de paragem](./media/r-train-score-model-create-quickstart/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> Neste roteiro de exemplo, a função é adicionada durante a fase de `str` teste para verificar o esquema de dados que estão sendo devolvidos de R. Pode retirar a declaração mais tarde.
>
> Os nomes das colunas utilizados no script R não são necessariamente transmitidos para a saída do procedimento armazenado. Aqui a cláusula COM RESULTADOS define alguns novos nomes de colunas.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre os Serviços de Machine Learning de Máquinas de Base de Dados Azure SQL com R (pré-visualização), consulte os seguintes artigos.

- [Serviços de Machine Learning de Base de Dados Azure SQL com R (pré-visualização)](machine-learning-services-overview.md)
- [Criar e executar scripts R simples em Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)](r-script-create-quickstart.md)
- [Escreva funções R avançadas na Base de Dados Azure SQL utilizando serviços de aprendizagem automática (pré-visualização)](machine-learning-services-functions.md)
- [Trabalhar com dados R e SQL nos Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)](machine-learning-services-data-issues.md)
