---
title: Analisar dados com o Azure Machine Learning
description: Utilize a Azure Machine Learning para construir um modelo preditivo de aprendizagem automática com base em dados armazenados em Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 76a0e4660967dafec8e314fd681d05e694e562b1
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85368197"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analisar dados com o Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Este tutorial utiliza a Azure Machine Learning para construir um modelo preditivo de aprendizagem automática com base em dados armazenados em Azure Synapse. Mais especificamente, cria uma campanha de marketing direcionada para Adventure Works, a loja de bicicletas, ao prever se existe ou não probabilidade de um cliente comprar uma bicicleta.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este tutorial, é necessário:

* Uma piscina SQL pré-carregada com dados de amostras AdventureWorksDW. Para o providenciar, consulte [Criar uma piscina SQL](create-data-warehouse-portal.md) e optar por carregar os dados da amostra. Se já tiver um armazém de dados, mas não tiver dados de exemplo, pode [carregar dados de exemplo manualmente](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-get-the-data"></a>1. Obter os dados
Os dados encontram-se na vista dbo.vTargetMail na base de dados AdventureWorksDW. Para ler estes dados:

1. Inicie sessão no [Azure Machine Learning studio](https://studio.azureml.net/) e clique em as minhas experimentações.
2. Clique **em +NEW** na parte inferior esquerda do ecrã e selecione **Blank Experiment**.
3. Introduza um nome para a experimentação: Marketing Direcionado.
4. Arraste o módulo **de dados de importação** sob a entrada de **dados e** a saída do painel de módulos para a tela.
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

1. Arraste as **colunas selecionais no** módulo Dataset sob **a transformação de dados < manipulação** para a tela. Ligue este módulo ao módulo **de Dados de Importação.**
2. Clique em **Iniciar seletor de colunas** no painel Propriedades, para especificar as colunas que pretende remover.

   ![Colunas do Projeto](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. Exclua duas colunas: CustomerAlternateKey e GeographyKey.

   ![Remover colunas desnecessárias](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. Construir o modelo
Iremos dividir os dados 80-20: 80% para preparar um modelo de machine learning e 20% para testar o modelo. Vamos usar os algoritmos de "Duas Classes" para este problema de classificação binária.

1. Arraste o módulo **Dividir** para a tela.
2. No painel de propriedades, introduza 0,8 para Fração de linhas no primeiro conjunto de dados de saída.

   ![Dividir os dados em conjunto de preparação e teste](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. Arraste o módulo **Árvore de Decisões Elevada de Duas Classes** para a tela.
4. Arraste o módulo **do Modelo de Comboio** para a tela e especifique as entradas ligando-o à Árvore de **Decisão Reforçada de Duas Classes** (algoritmo ML) e **Split** (dados para treinar o algoritmo em) módulos. 

     ![Ligar o módulo Preparar Modelo](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. Em seguida, clique em **Iniciar seletor de colunas** no painel Propriedades. Selecione a coluna **BikeBuyer** como a coluna a prever.

   ![Selecionar Coluna a prever](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. Marque o modelo
Agora, iremos testar o desempenho do modelo em dados de teste. Iremos comparar o algoritmo escolhido com um algoritmo diferente, para ver qual tem o melhor desempenho.

1. Arraste o módulo **do Modelo de Pontuação** na tela e conecte-o aos módulos **Train Model** e **Split Data.**

   ![Pontuar o modelo](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. Arraste a **Máquina do Ponto Bayes de Duas Classes** para a tela de experimentação. Iremos comparar o desempenho deste algoritmo em comparação com a Árvore de Decisões Elevada de Duas Classes.
3. Copie e cole os módulos Preparar Modelo e Pontuar Modelo na tela.
4. Arraste o módulo **Avaliar Modelo** para a tela, para comparar os dois algoritmos.
5. **Executar** a experiência.

   ![Executar a experimentação](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. Clique na porta de saída na parte inferior do módulo Avaliar Modelo e clique em Visualizar.

   ![Visualizar os resultados da avaliação](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

As métricas fornecidas são a curva ROC, o diagrama de recuperação de precisão e a curva de elevação. Ao observar estas métricas, é possível ver que o primeiro modelo tem melhor desempenho do que o segundo. Para ver o que o primeiro modelo previu, clique na porta de saída do Modelo de Pontuação e clique em Visualizar.

![Visualize os resultados de pontuação](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

Verá mais duas colunas adicionadas ao conjunto de dados de teste.

* Probabilidades Classificadas: a probabilidade de um cliente ser comprador de uma bicicleta.
* Etiquetas Classificadas: a classificação efetuada pelo modelo – comprador de bicicleta (1) ou não (0). Este limiar de probabilidade para etiquetas está definido como 50% e pode ser ajustado.

Ao comparar a coluna BikeBuyer (real) com as Etiquetas Classificadas (predição), pode ver quão bom foi o desempenho do modelo. Em seguida, pode utilizar este modelo para fazer previsões para novos clientes e publicar este modelo como um serviço web ou escrever resultados de volta para a Azure Synapse.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como criar modelos preditivos de machine learning, consulte [Introdução ao Machine Learning no Azure](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml).