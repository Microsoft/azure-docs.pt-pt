---
title: Analisar dados com o Azure Machine Learning
description: Utilize o Azure Machine Learning para construir um modelo de aprendizagem automática preditivo baseado em dados armazenados em Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 07570fd456d7f5a75a6b5a3ee635605a5d40072a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350576"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analisar dados com o Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Este tutorial utiliza o Azure Machine Learning para construir um modelo de aprendizagem automática preditivo baseado em dados armazenados em Azure Synapse. Mais especificamente, cria uma campanha de marketing direcionada para Adventure Works, a loja de bicicletas, ao prever se existe ou não probabilidade de um cliente comprar uma bicicleta.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este tutorial, é necessário:

* Um pool SQL pré-carregado com dados da amostra AdventureWorksDW. Para fornecer isto, consulte [Criar um pool SQL](create-data-warehouse-portal.md) e optar por carregar os dados da amostra. Se já tiver um armazém de dados, mas não tiver dados de exemplo, pode [carregar dados de exemplo manualmente](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-get-the-data"></a>1. Obter os dados
Os dados encontram-se na vista dbo.vTargetMail na base de dados AdventureWorksDW. Para ler estes dados:

1. Inicie sessão no [Azure Machine Learning studio](https://studio.azureml.net/) e clique em as minhas experimentações.
2. Clique **+NEW** na parte inferior esquerda do ecrã e selecione **Blank Experiment**.
3. Introduza um nome para a experimentação: Marketing Direcionado.
4. Arraste o módulo de **dados de Importação** sob a entrada de dados e a **saída** dos módulos para a tela.
5. Especifique os detalhes da sua piscina SQL no painel Propriedades.
6. Especifique a **consulta** de base de dados para ler os dados de interesse.

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

Execute experimentação ao clicar em **Executar** na tela de experimentação.

![Executar a experimentação](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

Depois de a execução da experimentação ser concluída com êxito, clique na porta de saída na parte inferior do módulo Leitor e selecione **Visualizar** para ver os dados importados.

![Ver os dados importados](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. Limpar os dados
Para limpar os dados, remova algumas colunas que não sejam relevantes para o modelo. Para efetuar este procedimento:

1. Arraste as **Colunas Selecionadas no** módulo Dataset sob **a transformação de dados < manipulação** para a tela. Ligue este módulo ao módulo **dados de importação.**
2. Clique em **Iniciar seletor de colunas** no painel Propriedades, para especificar as colunas que pretende remover.

   ![Colunas do Projeto](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. Exclua duas colunas: CustomerAlternateKey e GeographyKey.

   ![Remover colunas desnecessárias](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. Construir o modelo
Iremos dividir os dados 80-20: 80% para preparar um modelo de machine learning e 20% para testar o modelo. Faremos uso dos algoritmos de "Duas Classes" para este problema de classificação binária.

1. Arraste o módulo **Dividir** para a tela.
2. No painel de propriedades, introduza 0,8 para Fração de linhas no primeiro conjunto de dados de saída.

   ![Dividir os dados em conjunto de preparação e teste](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. Arraste o módulo **Árvore de Decisões Elevada de Duas Classes** para a tela.
4. Arraste o módulo **Train Model** para a tela e especifique as inputs ligando-o aos módulos de duas **classes boosted Decision Tree** (algoritmo ML) e **Split** (dados para treinar o algoritmo). 

     ![Ligar o módulo Preparar Modelo](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. Em seguida, clique em **Iniciar seletor de colunas** no painel Propriedades. Selecione a coluna **BikeBuyer** como a coluna a prever.

   ![Selecionar Coluna a prever](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. Marque o modelo
Agora, iremos testar o desempenho do modelo em dados de teste. Iremos comparar o algoritmo escolhido com um algoritmo diferente, para ver qual tem o melhor desempenho.

1. Módulo de Modelo de **Pontuação** de Arrasto na tela e conectá-lo aos módulos **De Formação** modelo e **Dados Split.**

   ![Pontuar o modelo](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. Arraste a **Máquina do Ponto Bayes de Duas Classes** para a tela de experimentação. Iremos comparar o desempenho deste algoritmo em comparação com a Árvore de Decisões Elevada de Duas Classes.
3. Copie e cole os módulos Preparar Modelo e Pontuar Modelo na tela.
4. Arraste o módulo **Avaliar Modelo** para a tela, para comparar os dois algoritmos.
5. **Fazer** a experiência.

   ![Executar a experimentação](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. Clique na porta de saída na parte inferior do módulo Avaliar Modelo e clique em Visualizar.

   ![Visualizar os resultados da avaliação](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

As métricas fornecidas são a curva ROC, o diagrama de precisão e a curva de elevação. Ao observar estas métricas, é possível ver que o primeiro modelo tem melhor desempenho do que o segundo. Para ver o que o primeiro modelo previu, clique na porta de saída do Modelo de Pontuação e clique em Visualizar.

![Visualize os resultados de pontuação](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

Verá mais duas colunas adicionadas ao conjunto de dados de teste.

* Probabilidades Classificadas: a probabilidade de um cliente ser comprador de uma bicicleta.
* Etiquetas Classificadas: a classificação efetuada pelo modelo – comprador de bicicleta (1) ou não (0). Este limiar de probabilidade para etiquetas está definido como 50% e pode ser ajustado.

Ao comparar a coluna BikeBuyer (real) com as Etiquetas Classificadas (predição), pode ver quão bom foi o desempenho do modelo. Em seguida, pode utilizar este modelo para fazer previsões para novos clientes e publicar este modelo como um serviço web ou escrever resultados de volta ao Azure Synapse.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como criar modelos preditivos de machine learning, consulte [Introdução ao Machine Learning no Azure](https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/).