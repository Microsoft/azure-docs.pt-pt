---
title: Marque modelos de aprendizagem automática com PREVISÃO
description: Aprenda a marcar modelos de machine learning utilizando a função T-SQL PREDICT em Synapse SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: anjangsh
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: ef56274e0bda3f1a9d494852520a77ecdfc25799
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89048011"
---
# <a name="score-machine-learning-models-with-predict"></a>Marque modelos de aprendizagem automática com PREVISÃO

O Synapse SQL proporciona-lhe a capacidade de marcar modelos de machine learning utilizando o familiar idioma T-SQL. Com o T-SQL [PREDICT,](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)pode trazer os seus modelos de machine learning existentes treinados com dados históricos e marcá-los dentro dos limites seguros do seu armazém de dados. A função PREDICT requer um modelo [ONNX (Open Neural Network Exchange)](https://onnx.ai/) e os dados como entradas. Esta funcionalidade elimina o passo de mover dados valiosos para fora do armazém de dados para pontuação. Tem como objetivo capacitar os profissionais de dados a implementarem facilmente modelos de machine learning com a interface T-SQL familiar, bem como colaborar perfeitamente com os cientistas de dados que trabalham com o enquadramento certo para a sua tarefa.

> [!NOTE]
> Esta funcionalidade não é suportada atualmente em SQL on-demand.

A funcionalidade requer que o modelo seja treinado fora do Synapse SQL. Depois de construir o modelo, carregue-o no armazém de dados e marque-o com a sintaxe T-SQL Predict para obter informações a partir dos dados.

![previsão sobre a visão de transição](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>Treinamento do Modelo

A Synapse SQL espera um modelo pré-treinado. Tenha em mente os seguintes fatores ao treinar um modelo de machine learning que é usado para realizar previsões em Synapse SQL.

- O Synapse SQL suporta apenas modelos de formato ONNX. O ONNX é um formato de modelo de código aberto que permite trocar modelos entre várias estruturas para permitir a interoperabilidade. Pode converter os seus modelos existentes no formato ONNX utilizando quadros que o suportem de forma nativa ou tenham pacotes de conversão disponíveis. Por exemplo, o pacote [sklearn-onnx](https://github.com/onnx/sklearn-onnx) converte modelos de aprendizagem de scikit para ONNX. [O repositório ONNX GitHub](https://github.com/onnx/tutorials#converting-to-onnx-format) apresenta uma lista de quadros e exemplos apoiados.

   Se estiver a utilizar [ml automatizado](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) para treino, certifique-se de definir o parâmetro *enable_onnx_compatible_models* para TRUE para produzir um modelo de formato ONNX. O Notebook automatizado de [aprendizagem automática](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) mostra um exemplo de como usar o AutoML para criar um modelo de aprendizagem automática do formato ONNX.

- Os seguintes tipos de dados são suportados para os dados de entrada:
    - int, bigint, real, flutuar
    - char, varchar, nvarchar

- Os dados de pontuação devem estar no mesmo formato que os dados de treino. Tipos de dados complexos, tais como matrizes multidimensionais, não são suportados pelo PREDICT. Assim, para o treino, certifique-se de que cada entrada do modelo corresponde a uma única coluna da tabela de pontuação em vez de passar uma única matriz contendo todas as entradas.

- Certifique-se de que os nomes e tipos de dados das entradas do modelo correspondem aos nomes das colunas e tipos de dados dos novos dados de previsão. Visualizar um modelo ONNX utilizando várias ferramentas de código aberto disponíveis online pode ajudar ainda mais na depuração.

## <a name="loading-the-model"></a>Carregar o modelo

O modelo é armazenado numa mesa de utilizador Synapse SQL como uma corda hexamal. Colunas adicionais como ID e descrição podem ser adicionadas na tabela do modelo para identificar o modelo. Utilize o varbinário(máx) como tipo de dados da coluna-modelo. Aqui está um exemplo de código para uma tabela que pode ser usada para armazenar modelos:

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

Uma vez que o modelo é convertido para uma corda hexadémica e a definição de tabela especificada, utilize o [comando COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) ou Polybase para carregar o modelo na tabela Synapse SQL. A seguinte amostra de código utiliza o comando Copy para carregar o modelo.

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>Marcando o modelo

Uma vez que o modelo e os dados são carregados no armazém de dados, utilize a função **T-SQL PREDICT** para marcar o modelo. Certifique-se de que os novos dados de entrada estão no mesmo formato que os dados de formação utilizados para a construção do modelo. T-SQL PREDICT requer duas entradas: modelo e novos dados de entrada de pontuação, e gera novas colunas para a saída. O modelo pode ser especificado como uma variável, uma sub_query literal ou escalar. Utilize [o WITH common_table_expression](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver15) para especificar um conjunto de resultados nomeado para o parâmetro de dados.

O exemplo abaixo mostra uma consulta de amostra usando a função de previsão. Uma coluna adicional com nome *Pontuação* e *boia* tipo de dados é criada contendo os resultados da previsão. Todas as colunas de dados de entrada, bem como as colunas de previsão de saída, estão disponíveis para visualizar com a declaração selecionada. Para mais detalhes, consulte [PREDICT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest).

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d) WITH (Score float) AS p;
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a função PREVISÃO, consulte [PREDICT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest).
