---
title: Analisar dados com o Azure Machine Learning
description: Utilize a Azure Machine Learning para construir um modelo preditivo de aprendizagem automática com base em dados armazenados em Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: ea4038e88d41a089958d4199e4c5a00f0d2acabd
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015571"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analisar dados com o Azure Machine Learning

Este tutorial usa [o designer de Aprendizagem automática Azure](https://docs.microsoft.com/azure/machine-learning/concept-designer) para construir um modelo preditivo de aprendizagem automática. O modelo baseia-se nos dados armazenados no Azure Synapse. O cenário para o tutorial é prever se um cliente é suscetível de comprar uma bicicleta ou não, para que a Adventure Works, a loja de bicicletas, possa construir uma campanha de marketing direcionada.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, é necessário:

* uma piscina SQL pré-carregada com dados de amostras AdventureWorksDW. Para provisões para este SQL Pool, consulte [Criar uma piscina SQL](create-data-warehouse-portal.md) e optar por carregar os dados da amostra. Se já tem um armazém de dados mas não tem dados de amostra, pode [carregar os dados da amostra manualmente.](load-data-from-azure-blob-storage-using-polybase.md)
* um espaço de trabalho de aprendizagem da Azure Machine. Siga [este tutorial](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) para criar um novo.

## <a name="get-the-data"></a>Obter os dados

Os dados utilizados estão na vista dbo.vTargetMail em AdventureWorksDW. Para utilizar a Datastore neste tutorial, os dados são exportados pela primeira vez para a conta de armazenamento do Lago de Dados Azure, uma vez que a Azure Synapse não suporta atualmente conjuntos de dados. A Azure Data Factory pode ser usada para exportar dados do armazém de dados para o Azure Data Lake Storage utilizando a atividade de [cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-overview). Utilize a seguinte consulta para importação:

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Uma vez que os dados estão disponíveis no Azure Data Lake Storage, datastores em Azure Machine Learning são usados para [ligar aos serviços de armazenamento Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data). Siga os passos abaixo para criar uma Datastore e um conjunto de dados correspondente:

1. Lance o estúdio Azure Machine Learning a partir do portal Azure ou inicie seduca no [estúdio Azure Machine Learning](https://ml.azure.com/).

1. Clique em **Datastores** no painel esquerdo na secção **Gestão** e clique em **New Datastore**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Screenshot do painel esquerdo da interface Azure Machine Learning":::

1. Forneça um nome para a loja de dados, selecione o tipo de 'Azure Blob Storage', forneça localização e credenciais. Em seguida, clique em **Criar**.

1. Em seguida, clique em **Conjuntos de Dados** no painel esquerdo na secção **Ativos.** Selecione **Criar conjunto de dados** com a opção **A partir da datastore**.

1. Especifique o nome do conjunto de dados e selecione o tipo de **Tabular**. Em seguida, clique **em Seguinte** para seguir em frente.

1. Em **Selecionar ou criar uma secção de datastore,** selecione a opção **previamente criada datastore**. Selecione a datastore que foi criada anteriormente. Clique em Seguinte e especifique as definições do caminho e do ficheiro. Certifique-se de especificar o cabeçalho da coluna se os ficheiros contiverem um.

1. Por fim, clique em **Criar** para criar o conjunto de dados.

## <a name="configure-designer-experiment"></a>Configure a experiência do designer

Em seguida, siga os passos abaixo para configuração do designer:

1. Clique no **separador Designer** no painel esquerdo na secção **Autor.**

1. Selecione **módulos pré-construídos fáceis de usar** para construir um novo oleoduto.

1. No painel de regulação à direita, especifique o nome da tubagem.

1. Além disso, selecione um cluster de cálculo alvo para toda a experiência no botão de definições para um cluster previamente provisionado. Feche o painel de definições.

## <a name="import-the-data"></a>Importar os dados

1. Selecione o subtab **datasets** no painel esquerdo abaixo da caixa de pesquisa.

1. Arraste o conjunto de dados que criou anteriormente para a tela.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="Screenshot do painel esquerdo da interface Azure Machine Learning":::

## <a name="clean-the-data"></a>Limpar os dados

Para limpar os dados, largam colunas que não são relevantes para o modelo. Siga os passos abaixo:

1. Selecione o subtab **de Módulos** no painel esquerdo.

1. Arraste as **colunas selecionais no** módulo Dataset sob **a transformação de dados < manipulação** para a tela. Ligue este módulo ao módulo **Dataset.**

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="Screenshot do painel esquerdo da interface Azure Machine Learning" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. Clique no módulo para abrir o painel de propriedades. Clique na coluna Editar para especificar quais as colunas que deseja deixar cair.

1. Exclua duas colunas: CustomerAlternateKey e GeographyKey. Clicar em **Guardar**

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="Screenshot do painel esquerdo da interface Azure Machine Learning":::

## <a name="build-the-model"></a>Construir o modelo

Os dados dividem-se 80-20: 80% para treinar um modelo de aprendizagem automática e 20% para testar o modelo. Algoritmos de "duas classes" são usados neste problema de classificação binária.

1. Arraste o módulo **de dados divididos** para a tela.

1. No painel de propriedades, introduza 0,8 para **fração de linhas no primeiro conjunto de dados de saída**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="Screenshot do painel esquerdo da interface Azure Machine Learning":::

1. Arraste o módulo **Árvore de Decisões Elevada de Duas Classes** para a tela.

1. Arraste o módulo **modelo de comboio** para a tela. Especifique as entradas ligando-as à **Árvore de Decisão Reforçada de Duas Classes** (algoritmo ML) e **dados divididos** (dados para treinar o algoritmo em) módulos.

1. Para o modelo train model, na opção **coluna Label** no painel Propriedades, selecione coluna Editar. Selecione a coluna **BikeBuyer** como a coluna para prever e selecionar **Guardar**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="Screenshot do painel esquerdo da interface Azure Machine Learning":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="Screenshot do painel esquerdo da interface Azure Machine Learning":::

## <a name="score-the-model"></a>Classificar o modelo

Agora, teste como o modelo funciona em dados de teste. Dois algoritmos diferentes serão comparados para ver qual deles tem um melhor desempenho. Siga os passos abaixo:

1. Arraste o módulo **do Modelo de Pontuação** na tela e conecte-o aos módulos **Train Model** e **Split Data.**

1. Arraste o **Pertron Averaged Perceptron de duas classes** para a tela de experimentação. Você vai comparar como este algoritmo funciona em comparação com a Two-Class Árvore de Decisão Reforçada.

1. Copie e cole os módulos **Modelo de Comboio** e Modelo de **Pontuação** na tela.

1. Arraste o módulo **Avaliar Modelo** para a tela, para comparar os dois algoritmos.

1. Clique **em submeter-se** para configurar o curso do gasoduto.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="Screenshot do painel esquerdo da interface Azure Machine Learning" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. Assim que a execução terminar, clique com o botão direito no módulo **Modelo de Avaliação** e clique nos **resultados da Avaliação visualização.**

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="Screenshot do painel esquerdo da interface Azure Machine Learning":::

As métricas fornecidas são a curva ROC, o diagrama de recuperação de precisão e a curva de elevação. Olhe para estas métricas para ver que o primeiro modelo teve um desempenho melhor do que o segundo. Para ver o que o primeiro modelo previu, clique com o botão direito no módulo 'Modelo de Pontuação' e clique em Visualizar Conjunto de dados Marcado para ver os resultados previstos.

Verá mais duas colunas adicionadas ao conjunto de dados do seu teste.

* Probabilidades Classificadas: a probabilidade de um cliente ser comprador de uma bicicleta.
* Etiquetas Classificadas: a classificação efetuada pelo modelo – comprador de bicicleta (1) ou não (0). Este limiar de probabilidade para etiquetas está definido como 50% e pode ser ajustado.

Compare a coluna BikeBuyer (real) com as Etiquetas Pontuadas (previsão), para ver como o modelo se apresentou bem. Em seguida, pode usar este modelo para fazer previsões para novos clientes. Pode [publicar este modelo como um serviço web](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) ou escrever resultados de volta à Azure Synapse.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Azure Machine Learning, consulte a [Introdução à Aprendizagem automática em Azure.](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)

Saiba mais sobre a pontuação incorporada no armazém de dados, [aqui.](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)