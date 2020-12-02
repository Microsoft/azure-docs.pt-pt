---
title: Começar com serviços cognitivos para big data
description: Confiúnio do seu pipeline MMLSpark com serviços cognitivos em Azure Databricks e execute uma amostra.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 095f2c3ed17042bb616fb091d1af52a64c913709
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460494"
---
# <a name="getting-started"></a>Introdução

Configurar o seu ambiente é o primeiro passo para a construção de um oleoduto para os seus dados. Depois de o ambiente estar pronto, a execução de uma amostra é rápida e fácil.

Neste artigo, vamos executar estes passos para começar:

1. [Criar um recurso dos Serviços Cognitivos](#create-a-cognitive-services-resource)
1. [Criar um Aglomerado de Faíscas Apache](#create-an-apache-spark-cluster)
1. [Experimente uma amostra](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

Para utilizar os Serviços Cognitivos big data, temos primeiro de criar um Serviço Cognitivo para o nosso fluxo de trabalho. Existem dois tipos principais de Serviços Cognitivos: serviços em nuvem hospedados em Azure e serviços contentorizados geridos pelos utilizadores. Recomendamos começar com os serviços cognitivos mais simples baseados na nuvem.

### <a name="cloud-services"></a>Serviços em nuvem

Os Serviços Cognitivos baseados na nuvem são algoritmos inteligentes alojados em Azure. Estes serviços estão prontos para serem utilizados sem formação, bastando uma ligação à Internet. Pode [criar um Serviço Cognitivo no portal Azure](../cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows) ou com o [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows).

### <a name="containerized-services-optional"></a>Serviços contentorizados (opcional)

Se a sua aplicação ou carga de trabalho utilizar grandes conjuntos de dados, requer networking privado, ou não conseguir contactar a nuvem, comunicar com serviços na nuvem pode ser impossível. Nesta situação, os Serviços Cognitivos contentorizados têm estes benefícios:

* **Baixa Conectividade**: Pode implantar serviços cognitivos contentorizados em qualquer ambiente de computação, tanto na nuvem como fora. Se a sua aplicação não puder contactar a nuvem, considere implantar serviços cognitivos contentorizados na sua aplicação.

* **Baixa Latência**: Como os serviços contentorizados não requerem a comunicação de ida e volta de/para a nuvem, as respostas são devolvidas com latências muito mais baixas.

* **Privacidade e Segurança de Dados**: Pode implantar serviços contentorizados em redes privadas, para que os dados sensíveis não saiam da rede.

* **Alta Escalabilidade**: Os serviços contentorizados não têm "limites de taxa" e funcionam em computadores geridos pelo utilizador. Então, você pode escalar os Serviços Cognitivos sem fim para lidar com cargas de trabalho muito maiores.

Siga [este guia](../cognitive-services-container-support.md?tabs=luis) para criar um Serviço Cognitivo contentorizado.

## <a name="create-an-apache-spark-cluster"></a>Criar um cluster do Apache Spark

[Faísca &trade; Apache](http://spark.apache.org/) é um quadro de computação distribuído projetado para o processamento de dados de grandes dados. Os utilizadores podem trabalhar com a Apache Spark em Azure com serviços como Azure Databricks, Azure Synapse Analytics, HDInsight e Azure Kubernetes Services. Para usar os Serviços Cognitivos big data, temos primeiro de criar um cluster. Se já tem um cluster Spark, sinta-se à vontade para experimentar um exemplo.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks é uma plataforma de análise baseada em Apache Spark com uma configuração de um clique, fluxos de trabalho simplificados e um espaço de trabalho interativo. É frequentemente usado para colaborar entre cientistas de dados, engenheiros e analistas de negócios. Para utilizar os Serviços Cognitivos big data em Azure Databricks, siga estes passos:

1. [Criar uma área de trabalho do Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [Criar um cluster do Spark no Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. Instalar os Serviços Cognitivos big data
    * Crie uma nova biblioteca no seu espaço de trabalho databricks  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * Insira as seguintes coordenadas maven:  `com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc3` Repositório: `https://mmlspark.azureedge.net/maven`  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * Instale a biblioteca num cluster  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="azure-synapse-analytics-optional"></a>Azure Synapse Analytics (opcional)

Opcionalmente, pode utilizar o Synapse Analytics para criar um cluster de faíscas. A Azure Synapse Analytics reúne o armazenamento de dados da empresa e a análise de big data. Dá-lhe a liberdade de consultar dados sobre os seus termos, utilizando recursos a pedido sem servidor ou a provisionados em escala. Para começar a usar a Azure Synapse Analytics, siga estes passos:

1. [Criar um espaço de trabalho sinapse (pré-visualização)](../../synapse-analytics/quickstart-create-workspace.md).
1. [Crie uma nova piscina Apache Spark sem servidor (pré-visualização) utilizando o portal Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

No Azure Synapse Analytics, o Big Data for Cognitive Services é instalado por padrão.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Se estiver a utilizar serviços cognitivos contentorizados, uma opção popular para implantar a Spark ao lado de contentores é o Serviço Azure Kubernetes.

Para começar no Serviço Azure Kubernetes, siga estes passos:

1. [Implementar um cluster do Azure Kubernetes Service (AKS) com o portal do Azure](../../aks/kubernetes-walkthrough-portal.md)
1. [Instale o gráfico de leme Apache Spark 2.4.0](https://hub.helm.sh/charts/microsoft/spark)
1. [Instale um recipiente de serviço cognitivo usando Helm](../computer-vision/deploy-computer-vision-on-premises.md)

## <a name="try-a-sample"></a>Experimente uma amostra

Depois de configurar o seu cluster spark e ambiente, podemos fazer uma amostra curta. Esta secção demonstra como usar o Big Data para serviços cognitivos em Azure Databricks.

Primeiro, podemos criar um caderno em Azure Databricks. Para outros fornecedores de cluster Spark, utilize os seus cadernos ou Envie a Spark Submit.

1. Crie um novo caderno Databricks, escolhendo **novo caderno** do menu **Azure Databricks.**

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. Na caixa de diálogo **Create Notebook,** insira um nome, selecione **Python** como o idioma e selecione o cluster Spark que criou anteriormente.

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    Selecione **Criar**.

1. Cole este código no seu novo caderno.

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. Obtenha a sua chave de subscrição no menu **Keys e Endpoint** do seu painel de texto Analytics no portal Azure.
1. Substitua o espaço reservado para a chave de subscrição no seu código de caderno Databricks pela sua chave de subscrição.
1. Selecione o símbolo de reprodução, ou triângulo, no canto superior direito da sua célula de portátil para executar a amostra. Opcionalmente, selecione **Executar Tudo** na parte superior do seu caderno para executar todas as células. As respostas serão exibidas abaixo da célula numa mesa.

### <a name="expected-results"></a>Resultados esperados

| texto                                      |   sentimento |
|:------------------------------------------|------------:|
| Estou tão feliz hoje, está ensolarado!           |   0.978959  |
| Estou frustrado com este tráfego de hora de ponta |   0.0237956 |
| Os serviços cognitivos na faísca aint mau  |   0.888896  |

## <a name="next-steps"></a>Passos seguintes

- [Exemplos de Python curtos](samples-python.md)
- [Exemplos curtos de Scala](samples-scala.md)
- [Receita: Manutenção Preditiva](recipes/anomaly-detection.md)
- [Receita: Exploração inteligente de arte](recipes/art-explorer.md)