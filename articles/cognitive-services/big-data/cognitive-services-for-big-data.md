---
title: Serviços Cognitivos para Macrodados
description: Aprenda a aproveitar os Serviços Cognitivos Azure em grandes conjuntos de dados usando Python, Java e Scala. Com serviços cognitivos para big data pode incorporar continuamente melhorando, modelos inteligentes diretamente em computações Apache Spark &trade; e SQL.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 07/09/2020
ms.author: marhamil
ms.openlocfilehash: 1b08925db12edffdaf5c85f1fa6f5934a412d81f
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363311"
---
# <a name="azure-cognitive-services-for-big-data"></a>Serviços Cognitivos Azure para Big Data

![Serviços Cognitivos Azure para Big Data](media/cognitive-services-big-data-overview.svg)

O Azure Cognitive Services for Big Data permite que os utilizadores canalizem terabytes de dados através dos Serviços Cognitivos utilizando [o Apache Spark. &trade; ](/dotnet/spark/what-is-spark) Com os Serviços Cognitivos para Big Data, é fácil criar aplicações inteligentes em larga escala com qualquer datastore.

Com serviços cognitivos para big data pode incorporar continuamente melhorando, modelos inteligentes diretamente em computações Apache Spark &trade; e SQL. Estas ferramentas libertam os desenvolvedores de detalhes de redes de baixo nível, para que possam concentrar-se na criação de aplicações inteligentes e distribuídas.

## <a name="features-and-benefits"></a>Características e benefícios

Os Serviços Cognitivos para Big Data podem utilizar serviços de qualquer região do mundo, bem como [serviços cognitivos contentorizados.](../cognitive-services-container-support.md) Os contentores suportam implementações de conectividade baixas ou sem respostas de latência ultra-baixas. Os Serviços Cognitivos contentorizados podem ser executados localmente, diretamente nos nós operários do seu cluster Spark, ou em um orquestrador externo como Kubernetes.

## <a name="supported-services"></a>Serviços suportados

[Os Serviços Cognitivos](../index.yml), acedidos através de APIs e SDKs, ajudam os desenvolvedores a construir aplicações inteligentes sem ter inteligência em IA ou em ciências de dados. Com os Serviços Cognitivos pode fazer com que as suas aplicações vejam, ouçam, falem, compreendam e raciocinem. Para utilizar os Serviços Cognitivos, a sua aplicação deve enviar dados para o serviço por toda a rede. Uma vez recebido, o serviço envia uma resposta inteligente em troca. Estão disponíveis os seguintes serviços para grandes cargas de trabalho de dados:

### <a name="vision"></a>Visão

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Imagem Digitalizada](../computer-vision/index.yml "Imagem Digitalizada")| O serviço De Visão De Computador fornece-lhe acesso a algoritmos avançados para processar imagens e devolver informações. |
|[Face](../face/index.yml "Face")| O serviço Face fornece acesso a algoritmos faciais avançados, permitindo a deteção e reconhecimento de atributos faciais. |

### <a name="speech"></a>Voz

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Serviço de voz](../speech-service/index.yml "Serviço de voz")|O serviço Discurso fornece acesso a funcionalidades como reconhecimento de voz, síntese de fala, tradução da fala e verificação e identificação de oradores.|

### <a name="decision"></a>Decisão

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Detetor de Anomalias](../anomaly-detector/index.yml "Detetor de Anomalias") | O serviço Detetor de Anomalias (Pré-visualização) permite-lhe monitorizar e detetar anomalias nos dados da série de tempo.|

### <a name="language"></a>Linguagem

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Análise de Texto](../text-analytics/index.yml "Análise de Texto")| O serviço Text Analytics fornece o processamento de linguagem natural sobre texto cru para análise de sentimentos, extração de frase-chave e deteção de linguagem.|

### <a name="search"></a>Pesquisar

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Pesquisa de Imagens do Bing](/azure/cognitive-services/bing-image-search "Pesquisa de Imagens do Bing")|O serviço Bing Image Search devolve uma exibição de imagens determinadas como relevantes para a consulta do utilizador.|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>Linguagens de programação apoiadas para serviços cognitivos para big data

Os Serviços Cognitivos para Big Data são construídos em Apache Spark. Apache Spark é uma biblioteca de computação distribuída que suporta Java, Scala, Python, R, e muitas outras línguas. Estas línguas são atualmente apoiadas.

### <a name="python"></a>Python

Fornecemos uma API PySpark no `mmlspark.cognitive` espaço de nome do Microsoft [ML para Apache Spark.](https://aka.ms/spark) Para mais informações, consulte a [API do Desenvolvedor python.](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html) Para exemplos de utilização, consulte as [Amostras de Python.](samples-python.md)

### <a name="scala-and-java"></a>Scala e Java

Fornecemos uma API de faíscas com base em Scala e Java no `com.microsoft.ml.spark.cognitive` espaço de nome do Microsoft [ML para Apache Spark.](https://aka.ms/spark) Para mais informações, consulte a [API do Desenvolvedor de Scala.](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package) Para exemplos de utilização, consulte as [Amostras de Scala.](samples-scala.md)

## <a name="supported-platforms-and-connectors"></a>Plataformas e conectores apoiados

Os Serviços Cognitivos para Big Data requerem Apache Spark. Existem várias plataformas Apache Spark que suportam os Serviços Cognitivos para Big Data.

### <a name="azure-databricks"></a>Azure Databricks

O [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) é uma plataforma de análise baseada no Apache Spark e otimizada para a plataforma de serviços cloud Microsoft Azure. Fornece uma configuração de um clique, fluxos de trabalho simplificados e um espaço de trabalho interativo que suporta a colaboração entre cientistas de dados, engenheiros de dados e analistas de negócios.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[A Azure Synapse Analytics](/azure/databricks/data/data-sources/azure/synapse-analytics) (anteriormente SQL Data Warehouse) é como um armazém de dados da empresa que usa um processamento paralelo maciço. Com o Synapse Analytics, você pode executar rapidamente consultas complexas através de petabytes de dados. A Azure Synapse Analytics fornece piscinas de faíscas geridas para executar Spark Jobs com uma interface de caderno jupyter intuitiva.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

[O Azure Kubernetes Service (AKS)](../../aks/index.yml) orquestra os contentores Docker e distribui aplicações em escalas maciças. AKS é uma oferta gerida de Kubernetes que simplifica a utilização de Kubernetes em Azure. Kubernetes pode permitir o controlo fino da escala de serviço cognitivo, latência e networking. No entanto, recomendamos a utilização de Azure Databricks ou Synapse Analytics se não estiver familiarizado com o Apache Spark.

### <a name="data-connectors"></a>Conectores de dados

Uma vez que tenha um Cluster de Faíscas, o próximo passo é ligar-se aos seus dados. A Apache Spark tem uma vasta coleção de conectores de base de dados. Estes conectores permitem que as aplicações funcionem com grandes conjuntos de dados, independentemente do local onde estejam armazenados. Para obter mais informações sobre bases de dados e conectores suportados, consulte a [lista de fontes de dados suportadas para Azure Databricks](/azure/databricks/data/data-sources/).

## <a name="concepts"></a>Conceitos

### <a name="spark"></a>Spark

[Faísca &trade; Apache](http://spark.apache.org/) é um motor de análise unificado para o processamento de dados em larga escala. A sua estrutura de processamento paralelo impulsiona o desempenho de grandes dados e aplicações analíticas. A faísca pode funcionar como um sistema de processamento de lote e fluxo, sem alterar o código de aplicação principal.

A base da Spark é o DataFrame: uma recolha tabular de dados distribuídos pelos nós dos trabalhadores da Apache Spark. Um DataFrame de Faísca é como uma tabela numa base de dados relacional ou um quadro de dados em R/Python, mas com escala ilimitada. DataFrames podem ser construídos a partir de muitas fontes, tais como: ficheiros de dados estruturados, tabelas na Colmeia ou bases de dados externas. Uma vez que os seus dados estão num DataFrame de Faísca, pode:

   - Faça computaçãos ao estilo SQL, tais como mesas de união e filtro.
   - Aplicar funções a grandes conjuntos de dados utilizando o paralelo do estilo MapReduce.
   - Aplicar machine learning distribuído usando o Microsoft Machine Learning para o Apache Spark.
   - Utilize os Serviços Cognitivos para Big Data para enriquecer os seus dados com serviços inteligentes prontos a usar.

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>Microsoft Machine Learning for Apache Spark (MMLSpark)

[Microsoft Machine Learning for Apache Spark](https://mmlspark.blob.core.windows.net/website/index.html#install) (MMLSpark) é uma biblioteca de aprendizagem automática distribuída (ML) de código aberto, construída em Apache Spark. Os Serviços Cognitivos para Big Data estão incluídos neste pacote. Além disso, o MMLSpark contém várias outras ferramentas ML para Apache Spark, tais como LightGBM, Vowpal Wabbit, OpenCV, LIME, e muito mais. Com o MMLSpark, pode construir poderosos modelos preditivos e analíticos a partir de qualquer fonte de dados Spark.

### <a name="http-on-spark"></a>HTTP em Faísca

Os Serviços Cognitivos para Big Data são um exemplo de como podemos integrar serviços web inteligentes com big data. Os serviços web alimentam muitas aplicações em todo o mundo e a maioria dos serviços comunicam através do Protocolo de Transferência de Hipertexto (HTTP). Para trabalhar com serviços web *arbitrários* em larga escala, fornecemos HTTP em Spark. Com HTTP em Spark, pode passar terabytes de dados através de qualquer serviço web. Sob o capot, usamos esta tecnologia para alimentar serviços cognitivos para big data.

## <a name="developer-samples"></a>Exemplos de programador

- [Receita: Manutenção Preditiva](recipes/anomaly-detection.md)
- [Receita: Exploração inteligente de arte](recipes/art-explorer.md)

## <a name="blog-posts"></a>Publicações no blogue

- [Saiba mais sobre como os Serviços Cognitivos funcionam na Apache Spark&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [Salvando leopardos da neve com aprendizagem profunda e visão computacional na faísca](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Microsoft Research Podcast: MMLSpark, capacitando AI para o bem com Mark Hamilton](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)
- [Papel Branco Académico: Microserviços Inteligentes em Larga Escala](https://arxiv.org/abs/2009.08044)

## <a name="webinars-and-videos"></a>Webinars e vídeos

- [Os Serviços Cognitivos Azure em Faísca: Clusters com Serviços Inteligentes Incorporados](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Spark Summit Keynote: IA escalável para o bem](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Os Serviços Cognitivos para Big Data em Cosmos DB](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)
- [Relâmpago fala em microserviços inteligentes de grande escala](https://www.youtube.com/watch?v=BtuhmdIy9Fk&t=6s)

## <a name="next-steps"></a>Passos seguintes

- [Começar com os Serviços Cognitivos para Big Data](getting-started.md)
- [Exemplos simples de Python](samples-python.md)
- [Exemplos simples de Scala](samples-scala.md)