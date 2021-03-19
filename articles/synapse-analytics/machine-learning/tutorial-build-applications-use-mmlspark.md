---
title: 'Tutorial: Construa aplicações de machine learning utilizando o Microsoft Machine Learning for Apache Spark (Preview)'
description: Aprenda a usar o Microsoft Machine Learning para o Apache Spark para criar aplicações de machine learning no Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruxu
ms.author: ruxu
ms.openlocfilehash: a3899b83133b3f951547fae0b11c044bfa85a5fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589604"
---
# <a name="tutorial-build-machine-learning-applications-using-microsoft-machine-learning-for-apache-spark-preview"></a>Tutorial: Construa aplicações de machine learning utilizando o Microsoft Machine Learning for Apache Spark (Preview)

Neste artigo, você aprenderá a usar o Microsoft Machine Learning para Apache Spark[(MMLSpark)](https://github.com/Azure/mmlspark)para criar aplicações de machine learning. O MMLSpark expande a solução de aprendizagem automática distribuída do Apache Spark adicionando muitas ferramentas de aprendizagem profunda e de ciência de dados, tais como [Azure Cognitive Services](../../cognitive-services/big-data/cognitive-services-for-big-data.md), [OpenCV,](https://opencv.org/) [LightGBM](https://github.com/Microsoft/LightGBM) e muito mais.  O MMLSpark permite-lhe construir modelos preditivos e analíticos poderosos e altamente escaláveis de várias fontes de dados spark.
A Synapse Spark fornece bibliotecas MMLSpark incorporadas, incluindo:

- [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit) – Serviços de biblioteca para machine learning para permitir análise de texto como análise de sentimento em tweets.
- [Serviços Cognitivos na Spark](../../cognitive-services/big-data/cognitive-services-for-big-data.md) – Combinar a característica dos Serviços Cognitivos Azure em oleodutos SparkML de forma a obter o design de solução para serviços de modelação de dados cognitivos como a deteção de anomalias.
- [LightBGM](https://github.com/Azure/mmlspark/blob/master/docs/lightgbm.md) – Modelo de aprendizagem automática para permitir a formação do modelo para análise preditiva como a deteção de ID facial.
- KNN condicional - Modelos KNN escaláveis com consultas condicionais.
- [HTTP on Spark](https://github.com/Azure/mmlspark/blob/master/docs/http.md) – Permite a orquestração distribuída de Microservices na integração da acessibilidade baseada no protocolo Spark e HTTP.

Este tutorial cobre amostras usando Azure Cognitive Services em MMLSpark para 

- Text Analytics - obtenha o sentimento (ou humor) de um conjunto de frases.
- Computer Vision - obtenha as etiquetas (descrições de uma palavra) associadas a um conjunto de imagens.
- Bing Image Search - procure na web imagens relacionadas com uma consulta de linguagem natural.
- Detetor de Anomalias - detete anomalias dentro de dados de séries de tempo.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos 

- [Espaço de trabalho Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento Azure Data Lake Gen2 configurada como o armazenamento padrão. Você precisa ser o colaborador de *dados blob* de armazenamento do sistema de ficheiros Data Lake Storage Gen2 com o que trabalha.
- Piscina de faíscas no seu espaço de trabalho Azure Synapse Analytics. Para mais detalhes, consulte [Criar uma piscina de faíscas em Azure Synapse.](../quickstart-create-sql-pool-studio.md)
- Passos de pré-configuração descritos nos serviços cognitivos de configuração tutorial [em Azure Synapse](./tutorial-configure-cognitive-services-synapse.md).


## <a name="get-started"></a>Introdução
Para começar, importe mmlspark e configure chaves de serviço.

```python
import mmlspark
mmlspark.__spark_package_version__ # current version: 1.0.0-rc3-6-a862d6b1-SNAPSHOT

from mmlspark.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY" 
# An Anomaly Dectector subscription key
anomaly_key =  "ADD_YOUR_SUBSCRIPION_KEY" 


cognitive_service_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", service_key)
bingsearch_service_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", bing_search_key)
anomalydetector_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", anomaly_key)

```

## <a name="text-analytics-sample"></a>Amostra de análise de texto

O serviço [Text Analytics](../../cognitive-services/text-analytics/index.yml) fornece vários algoritmos para extrair insights inteligentes do texto. Por exemplo, podemos encontrar o sentimento de texto de entrada dado. O serviço devolverá uma pontuação entre 0,0 e 1.0, onde as pontuações baixas indicam sentimento negativo e a pontuação elevada indica um sentimento positivo. Esta amostra usa três frases simples e devolve o sentimento para cada um.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>Resultados esperados

|texto | sentimento|
|--|--|
| Estou frustrado com este tráfego de hora de ponta! | negativo |
| este é um cão | neutro |
| Estou tão feliz hoje, está ensolarado! | positivo |

## <a name="computer-vision-sample"></a>Amostra de visão computacional
[A Visão Computacional](../../cognitive-services/computer-vision/index.yml) analisa imagens para identificar estruturas como rostos, objetos e descrições de linguagem natural. Nesta amostra, marcamos a imagem seguinte. Etiquetas são descrições de uma palavra das coisas na imagem como objetos reconhecíveis, pessoas, cenários e ações.


![image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>Resultados esperados

|image | etiquetas|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [patinação, pessoa, homem, ao ar livre, equitação, esporte, skate, jovem, prancha, camisa, ar, parque, menino, lado, pulando, rampa, truque, fazendo, voando] |

## <a name="bing-image-search-sample"></a>Amostra de pesquisa de imagem Bing
[Bing Image Search](../../cognitive-services/bing-image-search/overview.md) procura na web para recuperar imagens relacionadas com a consulta de linguagem natural de um utilizador. Nesta amostra, usamos uma consulta de texto que procura imagens com citações. Devolve uma lista de URLs de imagem que contêm fotos relacionadas com a nossa consulta.


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>Resultados esperados

|image | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>Amostra de detetor de anomalias

[O Detetor de Anomalias](../../cognitive-services/anomaly-detector/index.yml) é ótimo para detetar irregularidades nos dados da série de tempo. Nesta amostra, usamos o serviço para encontrar anomalias em toda a série de tempo.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>Resultados esperados

|carimbo de data/hora | valor | isAnomaly |
|--|--|--|
| 1972-01-01T00:00:00Z|826.0|false|
|1972-02-01T00:00:00Z|799.0|false|
|1972-03-01T00:00:00Z|890.0|false|
|1972-04-01T00:00:00Z|900.0|false|
|1972-05-01T00:00:00Z|766.0|false|
|1972-06-01T00:00:00Z|805.0|false|
|1972-07-01T00:00:00Z|821.0|false|
|1972-08-01T00:00:00Z|20000.0|true|
|1972-09-01T00:00:00Z|883.0|false|
|1972-10-01T00:00:00Z|898.0|false|
|1972-11-01T00:00:00Z|957.0|false|
|1972-12-01T00:00:00Z|924.0|false|
|1973-01-01T00:00:00Z|881.0|false|
|1973-02-01T00:00:00Z|837.0|false|
|1973-03-01T00:00:00Z|9000.0|true|

## <a name="next-steps"></a>Passos seguintes

* [Confira os cadernos da amostra da Sinapse](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks) 
* [MMLSpark GitHub Repo](https://github.com/Azure/mmlspark)