---
title: 'Tutorial: Deteção de anomalias com serviços cognitivos'
description: Tutorial para alavancar serviços cognitivos para deteção de anomalias na Sinapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 5e7b914d459d2452704f93987ce1bf91bfba988c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222212"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Tutorial: Deteção de anomalias com Serviços Cognitivos (Pré-visualização)

Neste tutorial, você aprenderá a enriquecer facilmente os seus dados em Azure Synapse com [Serviços Cognitivos.](../../cognitive-services/index.yml) Vamos usar o [Detetor de Anomalias](../../cognitive-services/anomaly-detector/index.yml) para fazer a deteção de anomalias. Um utilizador em Azure Synapse pode simplesmente selecionar uma tabela para enriquecer para deteção de anomalias.

Este tutorial aborda:

> [!div class="checklist"]
> - Passos para obter um conjunto de dados de tabela Spark contendo dados da série de tempo.
> - Utilize uma experiência de assistente em Azure Synapse para enriquecer dados usando o Serviço Cognitivo do Detetor de Anomalias.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Espaço de trabalho Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento ADLS Gen2 configurada como o armazenamento predefinido. Tem de ser o colaborador de dados da **Blob** de Armazenamento do sistema de ficheiros ADLS Gen2 com o que trabalha.
- Piscina de faíscas no seu espaço de trabalho Azure Synapse Analytics. Para mais detalhes, consulte [Criar uma piscina de faíscas em Azure Synapse.](../quickstart-create-sql-pool-studio.md)
- Antes de poder utilizar este tutorial, também precisa de completar os passos de pré-configuração descritos neste tutorial. [Configure serviços cognitivos em Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Criar uma mesa de faísca

Vai precisar de uma mesa spark para este tutorial.

1. Descarregue o seguinte ficheiro de caderno contendo código para gerar uma tabela Spark: [prepare_anomaly_detetor_data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149577)

1. Faça o upload do ficheiro para o seu espaço de trabalho Azure Synapse.
![Upload notebook](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Abra o ficheiro do caderno e opte por **executar todas as** células.
![Criar mesa spark](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Uma tabela Spark chamada **anomaly_detetor_testing_data** deve agora aparecer na base de dados padrão do Spark.

## <a name="launch-cognitive-services-wizard"></a>Assistente de Serviços Cognitivos de Lançamento

1. Clique à direita na tabela Spark criada no passo anterior. Selecione "Machine Learning-> Enriquecer com o modelo existente" para abrir o assistente.
![Assistente de pontuação de lançamento](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Aparecerá um painel de configuração e ser-lhe-á pedido que selecione um modelo de Serviços Cognitivos. Selecione detetor de anomalias.

![Assistente de pontuação de lançamento - passo1](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Fornecer detalhes de autenticação

Para autenticar os Serviços Cognitivos, é necessário fazer referência ao segredo a utilizar no seu Cofre de Chaves. As entradas abaixo dependem dos [passos pré-requisitos](tutorial-configure-cognitive-services-synapse.md) que deveria ter concluído antes deste passo.

- **Assinatura Azure**: Selecione a subscrição Azure a que pertence o cofre da chave.
- **Conta serviços cognitivos**: Este é o recurso Text Analytics a que se vai ligar.
- **Serviço ligado a azure Key Vault**: Como parte dos passos pré-requisitos, criou um serviço ligado ao seu recurso Text Analytics. Por favor, selecione aqui.
- **Nome secreto:** Este é o nome do segredo no seu cofre-chave contendo a chave para autenticar o seu recurso de Serviços Cognitivos.

![Fornecer detalhes do cofre da chave Azure](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detection"></a>Deteção de anomalias configuras

Em seguida, tens de configurar a deteção de anomalias. Forneça os seguintes detalhes:

- Granularidade: A taxa a que os seus dados são recolhidos. Por exemplo, se os seus dados têm um valor para cada minuto, então a sua granularidade seria minuciosa. Escolha **mensal** 

- Timetamp: Coluna representando o tempo da série. Escolha **a estamp de tempo da** coluna

- Valor dos tempos: Coluna que representa o valor da série no momento especificado pela coluna Timestamp. Escolha **o valor** da coluna

- Agrupamento: coluna que agrupar a série. Ou seja, todas as linhas que têm o mesmo valor nesta coluna devem formar uma série de tempo. Escolha o grupo de **colunas**

Uma vez feito, selecione **'Abrir' o caderno.** Isto irá gerar um caderno para si com o código PySpark que executa a deteção de anomalias usando os Serviços Cognitivos Azure.

![Detetor de anomalias de configuração](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="open-notebook-and-run"></a>Abrir caderno e correr

O caderno que acabou de abrir está a usar a [biblioteca mmlspark](https://github.com/Azure/mmlspark) para ligar aos serviços cognitivos.

Os detalhes do Azure Key Vault que forneceu permitem-lhe fazer referências seguras aos seus segredos a partir desta experiência sem os revelar.

Agora pode **executar todas as** células para efetuar a deteção de anomalias. Saiba mais sobre [serviços cognitivos - Detetor de Anomalias.](../../cognitive-services/anomaly-detector/index.yml)

![Deteção de anomalias de execução](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Análise de sentimento com serviços cognitivos Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Modelo de machine learning pontuação em Azure Synapse dedicada SQL Pools](tutorial-sql-pool-model-scoring-wizard.md)
- [Capacidades de aprendizagem automática em Azure Synapse Analytics](what-is-machine-learning.md)