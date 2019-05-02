---
title: Criar e formar um modelo preditivo em R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Criar um modelo preditivo simple no R usando os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização), em seguida, prever um resultado com dados novos.
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
ms.openlocfilehash: c1719064de53b79a127146d0ab034f461657cc64
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714903"
---
# <a name="create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Criar e formar um modelo preditivo em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)

Neste início rápido, vai criar e formar um modelo preditivo através da linguagem R, guardar o modelo para uma tabela na base de dados SQL e usar o modelo para prever valores de dados nova com a pré-visualização pública do [Machine Learning Services (com R) na base de dados do Azure SQL ](sql-database-machine-learning-services-overview.md). 

O modelo que irá utilizar neste início rápido é um modelo de regressão simples que prevê a distância de paragem de um automóvel com base na velocidade. Irá utilizar o **carros** conjunto de dados incluído com o R, porque é pequeno e fácil de entender.

> [!TIP]
> Estão incluídos muitos conjuntos de dados, pequenos e grandes, no runtime de R. Para obter uma lista de conjuntos de dados instalados com o R, escreva `library(help="datasets")` partir de uma linha de comando do R.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, [criar uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo Nestes exercícios, primeiro tem de ter uma SQL database do Azure com o Machine Learning Services (com R) ativada. Durante a pré-visualização pública, Microsoft irá carregar e ativar o machine learning para a base de dados nova ou existente. Siga os passos em [Inscreva-se na pré-visualização](sql-database-machine-learning-services-overview.md#signup).

- Certifique-se de que instalou a versão mais recente [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Pode executar os scripts R com outro gestão de bases de dados ou ferramentas de consulta, mas este início rápido irá utilizar o SSMS.

- Este início rápido requer que configure uma regra de firewall ao nível do servidor. Para obter informações sobre como fazer isso, consulte [criar regra de firewall ao nível do servidor](sql-database-server-level-firewall-rule.md).

## <a name="create-and-train-a-predictive-model"></a>Criar e formar um modelo preditivo

Os dados de velocidade de carro no **carros** conjunto de dados contém duas colunas, ambos os numérico: **dist** e **velocidade**. Os dados incluem várias observações de interrupção em velocidades diferentes. Partir destes dados, irá criar um modelo de regressão linear que descreve a relação entre a velocidade de carro e a distância necessários para parar um carro.

Os requisitos dos modelos lineares são simples:
- Definir uma fórmula que descreve a relação entre a variável dependente *velocidade* e a variável independente *distância*.
- Forneça os dados de entrada que vão ser utilizados na preparação do modelo.

> [!TIP]
> Se precisar de relembrar alguns aspetos sobre os modelos de lineares, experimente este tutorial que descreve o processo de que se ajusta a um modelo usando rxLinMod: [Que se ajusta modelos Linear](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

Nos passos seguintes que irá configurar os dados de treinamento, criar um modelo de regressão, formá-lo com os dados de treinamento, em seguida, guarde o modelo para uma tabela SQL.

1. Abra o **SQL Server Management Studio** e ligue à sua base de dados SQL.

   Se precisar de ajuda com a ligação, veja [início rápido: Utilizar o SQL Server Management Studio para se ligar e consultar uma base de dados SQL do Azure](sql-database-connect-query-ssms.md).

1. Criar a **CarSpeed** tabela para guardar os dados de treinamento.

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

1. Criar um modelo de regressão com `rxLinMod`. 

   Para criar o modelo de definir a fórmula dentro do código de R e, em seguida, passar os dados de treinamento **CarSpeed** como um parâmetro de entrada.

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

1. Crie uma tabela onde armazenar o modelo para que possa utilizá-lo mais tarde para predição. 

   O resultado de um pacote de R que cria um modelo é normalmente uma **objeto binário**, para que a tabela tem de ter uma coluna de **varbinary (Max)** tipo.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Agora, chamar o procedimento armazenado, gerar o modelo e guardá-lo a uma tabela.

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

## <a name="view-the-table-of-coefficients"></a>Veja a tabela de coeficientes

Geralmente, a saída de R do procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) está limitada a um único frame de dados. No entanto, pode devolver saídas de outros tipos, como escalares, para além do frame de dados.

Por exemplo, suponha que pretende preparar um modelo mas ver imediatamente a tabela de coeficientes do modelo. Para tal, crie a tabela de coeficientes porque o resultado principal definido e o modelo treinado numa variável SQL de saída. Pode imediatamente voltar a utilizar o modelo ao chamar a variável, ou pode guardar o modelo para uma tabela, conforme mostrado aqui.

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

![Modelo preparado com saída adicional](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Classificar dados novos com o modelo preparado

*Classificação* é um termo utilizado em ciência de dados para significar a geração de previsões de indisponibilidade, probabilidades ou outros valores com base nos dados novos inseridos num modelo preparado. Usará o modelo que criou na secção anterior para pontuar previsões contra dados novos.

Reparou que os dados de preparação originais param nos 45 quilómetros por hora? Isto deve-se ao facto de os dados originais terem por base uma experimentação de 1920! Deve estar imaginando, o tempo que seria necessário um automóvel do 1920s para parar de se ele poderia começar rapidamente como 60 mph ou até mesmo 100 mph? Para responder a essa pergunta, pode fornecer alguns novos valores de velocidade ao seu modelo.

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

2. Prever a distância de paragem desses novos valores de velocidade.

   Porque seu modelo se baseia a **rxLinMod** algoritmo fornecido como parte do **RevoScaleR** pacote, chama o [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) funcione, em vez do R genérico `predict` função.

   Este script de exemplo:
   - Utiliza uma instrução SELECT para obter um único modelo da tabela
   - Passa-o como um parâmetro de entrada
   - Chamadas a `unserialize` função no modelo
   - Aplica-se a `rxPredict` função com os argumentos adequados para o modelo
   - Fornece os dados de entrada novo

   > [!TIP]
   > Para a classificação em tempo real, consulte [funções de serialização](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) fornecidas por pacotes RevoScaleR.

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

   ![Conjunto de resultados para prever a distância de paragem](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> Neste script de exemplo, o `str` função é adicionada durante a fase de teste para verificar o esquema de dados que está a ser retornada do R. Pode remover a instrução mais tarde.
>
> Os nomes das colunas utilizados no script R não são necessariamente transmitidos para a saída do procedimento armazenado. Aqui a cláusula de resultados com define alguns novos nomes de coluna.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre serviços do Azure SQL Database Machine Learning com R (pré-visualização), consulte os artigos seguintes.

- [Serviços do Azure SQL da base de dados Machine Learning com R (pré-visualização)](sql-database-machine-learning-services-overview.md)
- [Criar e executar scripts simples do R no SQL da base de dados serviços Azure Machine Learning (pré-visualização)](sql-database-quickstart-r-create-script.md)
- [Escrever funções avançadas do R na base de dados do SQL Azure com serviços do Machine Learning (pré-visualização)](sql-database-machine-learning-services-functions.md)
- [Trabalhar com dados de R e SQL no SQL da base de dados serviços Azure Machine Learning (pré-visualização)](sql-database-machine-learning-services-data-issues.md)