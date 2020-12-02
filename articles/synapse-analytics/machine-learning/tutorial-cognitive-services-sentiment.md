---
title: 'Tutorial: Análise de sentimento com Serviços Cognitivos'
description: Tutorial para alavancar serviços cognitivos para análise de sentimento na Sinapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 1b407cbee5218149f794ab125ac058e32b422558
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96469064"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Tutorial: Análise de sentimento com Serviços Cognitivos (Pré-visualização)

Neste tutorial, você aprenderá a enriquecer facilmente os seus dados em Azure Synapse com [Serviços Cognitivos.](https://go.microsoft.com/fwlink/?linkid=2147492) Usaremos as capacidades [de Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) para realizar análise de sentimento. Um utilizador em Azure Synapse pode simplesmente selecionar uma tabela contendo uma coluna de texto para enriquecer com sentimentos. Estes sentimentos podem ser positivos, negativos, mistos ou neutros e uma probabilidade também será devolvida.

Este tutorial aborda:

> [!div class="checklist"]
> - Passos para obter um conjunto de dados de tabela Spark contendo coluna de texto para análise de sentimento.
> - Utilize uma experiência de assistente em Azure Synapse para enriquecer dados usando os Serviços Cognitivos Text Analytics.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Espaço de trabalho Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento ADLS Gen2 configurada como o armazenamento predefinido. Tem de ser o colaborador de dados da **Blob** de Armazenamento do sistema de ficheiros ADLS Gen2 com o que trabalha.
- Piscina de faíscas no seu espaço de trabalho Azure Synapse Analytics. Para mais detalhes, consulte [Criar uma piscina de faíscas em Azure Synapse.](../quickstart-create-sql-pool-studio.md)
- Antes de poder utilizar este tutorial, também precisa de completar os passos de pré-configuração descritos neste tutorial. [Configure serviços cognitivos em Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Criar uma mesa de faísca

Vai precisar de uma mesa spark para este tutorial.

1. Descarregue o seguinte ficheiro CSV contendo um conjunto de dados para análise de texto: [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv)

1. Faça o upload do ficheiro para a sua conta de armazenamento Azure Synapse ADLSGen2.
![Carregar dados](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Crie uma tabela Spark a partir do ficheiro .csv clicando no ficheiro e selecione **Nova tabela de > Criar Faíscas**.
![Criar mesa spark](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Diga o nome da tabela na célula de código e lance o caderno numa piscina de faíscas. Lembre-se de definir "cabeçalho = Verdadeiro".
![Bloco de Notas](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

```python
%%pyspark
df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
## If header exists uncomment line below
, header=True
)
df.write.mode("overwrite").saveAsTable("default.YourTableName")
```

## <a name="launch-cognitive-services-wizard"></a>Assistente de Serviços Cognitivos de Lançamento

1. Clique à direita na tabela Spark criada no passo anterior. Selecione "Machine Learning-> Enriquecer com o modelo existente" para abrir o assistente.
![Assistente de pontuação de lançamento](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Aparecerá um painel de configuração e ser-lhe-á pedido que selecione um modelo de Serviços Cognitivos. Selecione análise de texto - Análise de Sentimento.

![Assistente de pontuação de lançamento - passo1](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Fornecer detalhes de autenticação

Para autenticar os Serviços Cognitivos, é necessário fazer referência ao segredo a utilizar no seu Cofre de Chaves. As entradas abaixo dependem dos [passos pré-requisitos](tutorial-configure-cognitive-services-synapse.md) que deveria ter concluído antes deste passo.

- **Assinatura Azure**: Selecione a subscrição Azure a que pertence o cofre da chave.
- **Conta serviços cognitivos**: Este é o recurso Text Analytics a que se vai ligar.
- **Serviço ligado a azure Key Vault**: Como parte dos passos pré-requisitos, criou um serviço ligado ao seu recurso Text Analytics. Por favor, selecione aqui.
- **Nome secreto:** Este é o nome do segredo no seu cofre-chave contendo a chave para autenticar o seu recurso de Serviços Cognitivos.

![Fornecer detalhes do cofre da chave Azure](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Configure análise de sentimento

Em seguida, tens de configurar a análise de sentimentos. Por favor, selecione os seguintes detalhes:
- **Idioma**: Selecione a linguagem do texto em que pretende efetuar a análise de sentimento. Selecione **EN**.
- **Coluna de texto**: Esta é a coluna de texto no seu conjunto de dados que pretende analisar para determinar o sentimento. Selecione **comentário** da coluna de tabela .

Uma vez feito, clique em **Abrir O Caderno.** Isto irá gerar um caderno para si com o código PySpark que realiza a análise de sentimento com os Serviços Cognitivos Azure.

![Configure análise de sentimento](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="open-notebook-and-run"></a>Abrir caderno e correr

O caderno que acabou de abrir está a usar a [biblioteca mmlspark](https://github.com/Azure/mmlspark) para ligar aos serviços cognitivos.

Os detalhes do Azure Key Vault que forneceu permitem-lhe fazer referências seguras aos seus segredos a partir desta experiência sem os revelar.

Agora pode **executar todas as** células para enriquecer os seus dados com sentimentos. Os sentimentos serão devolvidos como Positivo/Negativo/Neutro/Misto, e também terá probabilidades por sentimento. Saiba mais sobre [Serviços Cognitivos - Análise de Sentimento.](https://go.microsoft.com/fwlink/?linkid=2147792)

![Executar análise de sentimento](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Passos seguintes
- [Tutorial: Deteção de anomalias com serviços cognitivos Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Modelo de machine learning pontuação em Azure Synapse dedicada SQL Pools](tutorial-sql-pool-model-scoring-wizard.md)
- [Capacidades de aprendizagem automática em Azure Azure Synapse Analytics](what-is-machine-learning.md)