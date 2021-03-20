---
title: 'Tutorial: Análise de sentimento com Serviços Cognitivos'
description: Saiba como usar os Serviços Cognitivos para análise de sentimentos no Azure Synapse Analytics
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 08d5e53facce172c2287c2e341895f0ee38571f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943708"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Tutorial: Análise de sentimento com Serviços Cognitivos (pré-visualização)

Neste tutorial, você aprenderá a enriquecer facilmente os seus dados em Azure Synapse Analytics com [Azure Cognitive Services](../../cognitive-services/index.yml). Utilizará as capacidades [de Análise de Texto](../../cognitive-services/text-analytics/index.yml) para realizar análise de sentimento. 

Um utilizador em Azure Synapse pode simplesmente selecionar uma tabela que contenha uma coluna de texto para enriquecer com sentimentos. Estes sentimentos podem ser positivos, negativos, mistos ou neutros. Uma probabilidade também será devolvida.

Este tutorial aborda:

> [!div class="checklist"]
> - Passos para obter um conjunto de dados de tabela Spark que contém uma coluna de texto para análise de sentimento.
> - Utilizando uma experiência de assistente em Azure Synapse para enriquecer dados utilizando o Text Analytics em Serviços Cognitivos.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Espaço de trabalho Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento Azure Data Lake Gen2 configurada como o armazenamento padrão. Você precisa ser o colaborador de *dados blob* de armazenamento do sistema de ficheiros Data Lake Storage Gen2 com o que trabalha.
- Piscina de faíscas no seu espaço de trabalho Azure Synapse Analytics. Para mais detalhes, consulte [Criar uma piscina de faíscas em Azure Synapse.](../quickstart-create-sql-pool-studio.md)
- Passos de pré-configuração descritos nos serviços cognitivos de configuração tutorial [em Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Criar uma mesa de faísca

Vai precisar de uma mesa de faísca para este tutorial.

1. Descarregue o ficheiro [FabrikamComments.csv, ](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv) que contém um conjunto de dados para análise de texto. 

1. Faça o upload do ficheiro para a sua conta de armazenamento Azure Synapse na Data Lake Storage Gen2.
  
   ![Screenshot que mostra seleções para o upload de dados.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Crie uma tabela Spark a partir do ficheiro .csv clicando no ficheiro e selecionando a tabela **New Notebook**  >  **Create Spark**.

   ![Screenshot que mostra seleções para criar uma mesa Spark.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Diga o nome da tabela na célula de código e lance o caderno numa piscina de faíscas. Lembre-se de definir `header=True` .

   ![Screenshot que mostra executar um caderno.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

   ```python
   %%pyspark
   df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
   ## If a header exists, uncomment the line below
   , header=True
   )
   df.write.mode("overwrite").saveAsTable("default.YourTableName")
   ```

## <a name="open-the-cognitive-services-wizard"></a>Abra o assistente dos Serviços Cognitivos

1. Clique com o botão direito na tabela Spark criada no procedimento anterior. Selecione **Machine Learning**  >  **Enrich com o modelo existente** para abrir o assistente.

   ![Screenshot que mostra seleções para abrir o assistente de pontuação.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Aparece um painel de configuração e é-lhe pedido que selecione um modelo de Serviços Cognitivos. Selecione **análise de texto - Análise de Sentimento**.

   ![Screenshot que mostra a seleção de um modelo de Serviços Cognitivos.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Fornecer detalhes de autenticação

Para autenticar os Serviços Cognitivos, é necessário fazer referência ao segredo do seu cofre chave. As seguintes entradas dependem dos [passos pré-requisitos](tutorial-configure-cognitive-services-synapse.md) que deveria ter concluído antes deste ponto.

- **Subscrição Azure**: Selecione a subscrição Azure a que pertence o cofre da chave.
- **Conta Serviços Cognitivos**: Introduza o recurso Text Analytics ao qual irá ligar-se.
- **Serviço ligado a Azure Key Vault**: Como parte dos passos pré-requisitos, criou um serviço ligado ao seu recurso Text Analytics. Selecione aqui.
- **Nome secreto**: Introduza o nome do segredo no seu cofre-chave que contém a chave para autenticar o seu recurso de Serviços Cognitivos.

![Screenshot que mostra detalhes de autenticação para um cofre chave.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Configure análise de sentimento

Em seguida, configurar a análise de sentimento. Selecione os seguintes detalhes:
- **Idioma**: Selecione o **inglês** como a língua do texto em que pretende realizar a análise de sentimento.
- **Coluna de texto**: Selecione **o comentário (cadeia)** como a coluna de texto no seu conjunto de dados que pretende analisar para determinar o sentimento.

Quando terminar, selecione **Abrir o caderno**. Isto gera um caderno para si com o código PySpark que realiza a análise de sentimento com os Serviços Cognitivos Azure.

![Screenshot que mostra seleções para configurar a análise de sentimento.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="run-the-notebook"></a>Executar o bloco de notas

O caderno que acaba de abrir usa a [biblioteca mmlspark](https://github.com/Azure/mmlspark) para ligar aos Serviços Cognitivos. Os detalhes do Azure Key Vault que forneceu permitem-lhe fazer referências seguras aos seus segredos a partir desta experiência sem os revelar.

Agora pode executar todas as células para enriquecer os seus dados com sentimentos. Selecione **Executar tudo**. 

Os sentimentos são devolvidos como **positivos,** **negativos,** **neutros** ou **misturados.** Também obtém probabilidades por sentimento. [Saiba mais sobre a análise de sentimentos nos Serviços Cognitivos.](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md)

![Screenshot que mostra análise de sentimento.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Passos seguintes
- [Tutorial: Deteção de anomalias com serviços cognitivos Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Modelo de machine learning pontuação em piscinas SQL dedicadas Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Capacidades de Machine Learning no Azure Synapse Analytics](what-is-machine-learning.md)