---
title: 'Tutorial: Deteção de anomalias com serviços cognitivos'
description: Saiba como utilizar os Serviços Cognitivos para deteção de anomalias no Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: c54300bf37f6f4526c525b1502d902e5f4336ed7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943505"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Tutorial: Deteção de anomalias com Serviços Cognitivos (pré-visualização)

Neste tutorial, você aprenderá a enriquecer facilmente os seus dados em Azure Synapse Analytics com [Azure Cognitive Services](../../cognitive-services/index.yml). Vais usar [o Detetor de Anomalias](../../cognitive-services/anomaly-detector/index.yml) para encontrar anomalias. Um utilizador em Azure Synapse pode simplesmente selecionar uma tabela para enriquecer para deteção de anomalias.

Este tutorial aborda:

> [!div class="checklist"]
> - Passos para obter um conjunto de dados de tabela Spark que contém dados da série de tempo.
> - Utilização de uma experiência de assistente em Azure Synapse para enriquecer dados utilizando detetor de anomalias em serviços cognitivos.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Espaço de trabalho Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento Azure Data Lake Gen2 configurada como o armazenamento padrão. Você precisa ser o colaborador de *dados blob* de armazenamento do sistema de ficheiros Data Lake Storage Gen2 com o que trabalha.
- Piscina de faíscas no seu espaço de trabalho Azure Synapse Analytics. Para mais detalhes, consulte [Criar uma piscina de faíscas em Azure Synapse.](../quickstart-create-sql-pool-studio.md)
- Conclusão dos passos de pré-configuração no tutorial [Configure Cognitive Services in Azure Synapse.](tutorial-configure-cognitive-services-synapse.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Criar uma mesa de faísca

Precisas de uma mesa de faíscas para este tutorial.

1. Descarregue o seguinte ficheiro de caderno que contém código para gerar uma tabela Spark: [prepare_anomaly_detetor_data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149577).

1. Faça o upload do ficheiro para o seu espaço de trabalho Azure Synapse.

   ![Screenshot que mostra seleções para carregar um caderno.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Abra o ficheiro do caderno e selecione **Executar Tudo** para executar todas as células.

   ![Screenshot que mostra seleções para criar uma mesa Spark.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Uma tabela Spark chamada **anomaly_detetor_testing_data** deve agora aparecer na base de dados padrão do Spark.

## <a name="open-the-cognitive-services-wizard"></a>Abra o assistente dos Serviços Cognitivos

1. Clique com o botão direito na tabela Spark criada no passo anterior. Selecione **Machine Learning**  >  **Enrich com o modelo existente** para abrir o assistente.

   ![Screenshot que mostra seleções para abrir o assistente de pontuação.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Aparece um painel de configuração e é-lhe pedido que selecione um modelo de Serviços Cognitivos. Selecione **Detetor de Anomalias**.

   ![Screenshot que mostra a seleção do Detetor de Anomalias como modelo.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Fornecer detalhes de autenticação

Para autenticar os Serviços Cognitivos, é necessário fazer referência ao segredo no cofre das chaves. As seguintes entradas dependem dos [passos pré-requisitos](tutorial-configure-cognitive-services-synapse.md) que deveria ter concluído antes deste ponto.

- **Subscrição Azure**: Selecione a subscrição Azure a que pertence o cofre da chave.
- **Conta Serviços Cognitivos**: Introduza o recurso Text Analytics ao qual irá ligar-se.
- **Serviço ligado a Azure Key Vault**: Como parte dos passos pré-requisitos, criou um serviço ligado ao seu recurso Text Analytics. Selecione aqui.
- **Nome secreto**: Introduza o nome do segredo no seu cofre-chave que contém a chave para autenticar o seu recurso de Serviços Cognitivos.

![Screenshot que mostra detalhes de autenticação para um cofre chave.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detector"></a>Detetor de anomalias configurar

Forneça os seguintes detalhes para configurar o Detetor de Anomalias:

- **Granularidade**: A taxa a que os seus dados são recolhidos. Escolha **mensalmente.** 

- **Coluna de relógios**: A coluna que representa o tempo da série. Escolha **o tempotamp (string)**.

- **Coluna de valor timeseries**: A coluna que representa o valor da série no momento especificado pela coluna Timestamp. Escolha **o valor (duplo)**.

- **Coluna de agrupamento**: A coluna que agrupar a série. Ou seja, todas as linhas que têm o mesmo valor nesta coluna devem formar uma série de tempo. Escolha **grupo (corda)**.

Quando terminar, selecione **Abrir o caderno**. Isto irá gerar um caderno para si com o código PySpark que utiliza os Serviços Cognitivos Azure para detetar anomalias.

![Screenshot que mostra detalhes de configuração para Detetor de Anomalias.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="run-the-notebook"></a>Executar o bloco de notas

O caderno que acaba de abrir usa a [biblioteca mmlspark](https://github.com/Azure/mmlspark) para ligar aos Serviços Cognitivos. Os detalhes do Azure Key Vault que forneceu permitem-lhe fazer referências seguras aos seus segredos a partir desta experiência sem os revelar.

Agora pode executar todas as células para realizar a deteção de anomalias. Selecione **Executar Tudo**. [Saiba mais sobre o Detetor de Anomalias nos Serviços Cognitivos.](../../cognitive-services/anomaly-detector/index.yml)

![Screenshot que mostra deteção de anomalias.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Próximos passos

- [Tutorial: Análise de sentimento com serviços cognitivos Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Modelo de machine learning pontuação em piscinas SQL dedicadas Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Capacidades de aprendizagem automática em Azure Synapse Analytics](what-is-machine-learning.md)
