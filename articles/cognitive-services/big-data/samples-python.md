---
title: Serviços Cognitivos para Big Data - Amostras de Python
description: Experimente amostras de Serviços Cognitivos em Python para que o Azure Databricks possa executar o seu pipeline MMLSpark para grandes dados.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 590ddef27315f37719da5b28c68b6c402371e986
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363260"
---
# <a name="python-samples-for-cognitive-services-for-big-data"></a>Amostras de Python para serviços cognitivos para big data

Os seguintes snippets estão prontos para correr e vão ajudá-lo a começar a usar serviços cognitivos em Spark com Python.

As amostras deste artigo utilizam estes Serviços Cognitivos:

- Text Analytics - obtenha o sentimento (ou humor) de um conjunto de frases.
- Computer Vision - obtenha as etiquetas (descrições de uma palavra) associadas a um conjunto de imagens.
- Bing Image Search - procure na web imagens relacionadas com uma consulta de linguagem natural.
- Speech-to-text - transcreva ficheiros áudio para extrair transcrições baseadas em texto.
- Detetor de Anomalias - detete anomalias dentro de dados de séries de tempo.

## <a name="prerequisites"></a>Pré-requisitos

1. Siga os passos em [Começar](getting-started.md) a configurar o seu ambiente Azure Databricks e Serviços Cognitivos. Este tutorial mostra-lhe como instalar o MMLSpark e como criar o seu cluster Spark em Databricks.
1. Depois de criar um novo caderno em Azure Databricks, copie o **código Partilhado** abaixo e cole-o numa nova célula no seu caderno.
1. Escolha uma amostra de serviço, abaixo, e copie-a para uma segunda célula nova no seu caderno.
1. Substitua qualquer um dos espaços reservados para a subscrição do serviço pela sua própria chave.
1. Escolha o botão de funcionação (ícone triângulo) no canto superior direito da célula e, em seguida, selecione **Run Cell**.
1. Ver resultados numa tabela abaixo da célula.

## <a name="shared-code"></a>Código compartilhado

Para começar, precisamos adicionar este código ao projeto:

```python
from mmlspark.cognitive import *

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key = "ADD_YOUR_SUBSCRIPION_KEY"
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY"
# An Anomaly Dectector subscription key
anomaly_key = "ADD_YOUR_SUBSCRIPION_KEY"

# Validate the key
assert service_key != "ADD_YOUR_SUBSCRIPION_KEY"
```    

## <a name="text-analytics-sample"></a>Amostra de Análise de Texto

O serviço [Text Analytics](../text-analytics/index.yml) fornece vários algoritmos para extrair insights inteligentes do texto. Por exemplo, podemos encontrar o sentimento de texto de entrada dado. O serviço devolverá uma pontuação entre 0,0 e 1.0, onde as pontuações baixas indicam sentimento negativo e a pontuação elevada indica um sentimento positivo.  Esta amostra usa três frases simples e devolve o sentimento para cada um.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format
display(sentiment.transform(df).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-result"></a>Resultado esperado

| texto                                      | sentimento                                             |
|:------------------------------------------|:------------------------------------------------------|
| Estou tão feliz hoje, está ensolarado!           | positivo                                              |
| Estou frustrado com este tráfego de hora de ponta | negativo                                              |
| Os serviços cognitivos na faísca aint mau  | positivo                                              |

## <a name="computer-vision-sample"></a>Amostra de Visão de Computador

[A Visão Computacional](../computer-vision/index.yml) analisa imagens para identificar estruturas como rostos, objetos e descrições de linguagem natural. Nesta amostra, marcamos uma lista de imagens. Etiquetas são descrições de uma palavra das coisas na imagem como objetos reconhecíveis, pessoas, cenários e ações.

```python

# Create a dataframe with the image URLs
df = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select("image", "analysis_results.description.tags"))
```

### <a name="expected-result"></a>Resultado esperado

| image | etiquetas |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ['skate' 'homem' 'homem' 'outdoor' 'riding' 'sport' 'skate' 'young' 'board' 'air' 'air' 'black' 'boy' 'side' 'jumping' 'trick' 'ramp' 'doing' 'flying']
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ['dog' 'outdoor' 'vedação' 'madeira' 'pequeno' 'brown' 'sentando's 'frente' 'banco' 'pé' 'mesa' 'tábua' 'board' 'praia' 'branco' 'segurando' 'ponte' 'pista']                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ['outdoor' 'erva' 'casa' 'edifício' 'casa antiga' 'casa' 'frente' 'pequena' 'igreja' 'pedra' 'grande' 'pastagem' 'quintal' 'verde' 'sentado' 'liderando' 'ovelha' 'tijolo» 'banco' 'rua' 'branco' 'relógio' 'relógio' 'sinal' 'estacionado' 'campo' 'jardim' 'água' 'cavalo' 'cavalo' 'alto' 'fogo' 'grupo']


## <a name="bing-image-search-sample"></a>Amostra de pesquisa de imagem de Bing

[Bing Image Search](../bing-image-search/overview.md) procura na web para recuperar imagens relacionadas com a consulta de linguagem natural de um utilizador. Nesta amostra, usamos uma consulta de texto que procura imagens com citações. Devolve uma lista de URLs de imagem que contêm fotos relacionadas com a nossa consulta.

```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 10
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(100)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bing_search_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")

# This displays the full results returned, uncomment to use
# display(bingSearch.transform(bingParameters))

# Since we have two services, they are put into a pipeline
pipeline = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
display(pipeline.transform(bingParameters))
```

### <a name="expected-result"></a>Resultado esperado

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |


## <a name="speech-to-text-sample"></a>Amostra de discurso para texto
O [serviço Discurso-a-Texto](../speech-service/index-speech-to-text.yml) converte streams ou ficheiros de áudio falado para texto. Nesta amostra, transcrevemos dois ficheiros áudio. O primeiro ficheiro é fácil de entender, e o segundo é mais desafiante.

```python

# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",),
                           ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```

### <a name="expected-result"></a>Resultado esperado

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | A fala personalizada fornece ferramentas que permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando os dados áudio com o resultado correspondente do reconhecimento do portal de fala personalizado. Pode reproduzir áudio carregado e determinar se o resultado do reconhecimento fornecido está correto. Esta ferramenta permite-lhe inspecionar rapidamente a qualidade do discurso de base da Microsoft para o modelo de texto ou um modelo personalizado treinado sem ter de transcrever quaisquer dados áudio. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Adicione um cavalheiro, Senhor, a pensar em verificação visual.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Estou a ouvir-me. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Gosto da garantia de que também posso ouvi-la. |


## <a name="anomaly-detector-sample"></a>Amostra de detetor de anomalias

[O Detetor de Anomalias](../anomaly-detector/index.yml) é ótimo para detetar irregularidades nos dados da série de tempo. Nesta amostra, usamos o serviço para encontrar anomalias em toda a série de tempo.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0),
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0)
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomaly_key)
  .setLocation("eastus")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
```

### <a name="expected-result"></a>Resultado esperado

| carimbo de data/hora            |   valor | isAnomaly   |
|:---------------------|--------:|:------------|
| 1972-01-01T00:00:00Z |     826 | Falso       |
| 1972-02-01T00:00:00Z |     799 | Falso       |
| 1972-03-01T00:00:00Z |     890 | Falso       |
| 1972-04-01T00:00:00Z |     900 | Falso       |
| 1972-05-01T00:00:00Z |     766 | Falso       |
| 1972-06-01T00:00:00Z |     805 | Falso       |
| 1972-07-01T00:00:00Z |     821 | Falso       |
| 1972-08-01T00:00:00Z |   20 000 | Verdadeiro        |
| 1972-09-01T00:00:00Z |     883 | Falso       |
| 1972-10-01T00:00:00Z |     898 | Falso       |
| 1972-11-01T00:00:00Z |     957 | Falso       |
| 1972-12-01T00:00:00Z |     924 | Falso       |
| 1973-01-01T00:00:00Z |     881 | Falso       |
| 1973-02-01T00:00:00Z |     837 | Falso       |
| 1973-03-01T00:00:00Z |    9000 | Verdadeiro        |

## <a name="arbitrary-web-apis"></a>APIs arbitrários da web

Com HTTP em Spark, qualquer serviço web pode ser usado no seu grande pipeline de dados. Neste exemplo, utilizamos a [API do Banco Mundial](http://api.worldbank.org/v2/country/) para obter informações sobre vários países em todo o mundo.

```python
from requests import Request
from mmlspark.io.http import HTTPTransformer, http_udf
from pyspark.sql.functions import udf, col

# Use any requests from the python requests library
def world_bank_request(country):
  return Request("GET", "http://api.worldbank.org/v2/country/{}?format=json".format(country))

# Create a dataframe with spcificies which countries we want data on
df = (spark.createDataFrame([("br",),("usa",)], ["country"])
  .withColumn("request", http_udf(world_bank_request)(col("country"))))

# Much faster for big data because of the concurrency :)
client = (HTTPTransformer()
      .setConcurrency(3)
      .setInputCol("request")
      .setOutputCol("response"))

# Get the body of the response
def get_response_body(resp):
  return resp.entity.content.decode()

# Show the details of the country data returned
display(client.transform(df).select("country", udf(get_response_body)(col("response")).alias("response")))
```

### <a name="expected-result"></a>Resultado esperado

| país   | resposta |
|:----------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| br | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"BRA","iso2Code":"BR","nome":"Brasil","região":{"id":"BRA","iso2Code":"BR","nome":"Brasil","região":{{"id":"BRA","iso2Code":"BR","nome":"Brasil","região":{{"id":"BRA","iso2Code":"BR","nome":"Brasil","região":{"id":"BRA","iso2Code":"BR","nome":"Brasil","região":{"id":"BRA","iso2Code":"BR",""Id":"LCN","iso2code":"ZJ","valor":"América Latina & Caraíbas "}", adminregion":{"id":"LAC","iso2code":"XJ","valor":"Latim":"Latim"America & Caraíbas (excluindo o rendimento elevado)"}"""""incomeLevel":{"id":"UMC","iso2code":"XT","value":"Rendimento médio superior"},"lendingType":{"id":"IBD "Iso2code":"XF","valor":"IBRD"}"capitalCity":"Brasília","longitude":"-47.9292","latitude":"-15.7801"}]] |
| EUA  | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"USA","iso2Code":"US","name":"United States","region"":{"Id":"NAC","iso2code":"XU","value":"América do Norte"}",adminregion":{"id":"id":"""iso2code":"XU","value":"América do Norte"}",adminregion":{"id":"""iso2code":"XU","valor":"América do Norte"}","adminregão":"id":"Id":"""Iso2code":"XU","valor":"América do Norte"}",adminregão":"id":"Id":""""Iso2code":"XU","valor":"América do Norte"}","adminregion":"{"id":"Id":"""""valor":""}""incomeLevel":{"id":"HIC","iso2code":"XD","Valor":"Alto rendimento"},"lendingType":{"id":"LNX","iso2code":"XX","valor":"Não classificado"},"capitalCity":"Washington D.C.""Longitude":"-77.032","latitude":"38.8895"}]} |

## <a name="see-also"></a>Ver também

* [Receita: Deteção de Anomalias](./recipes/anomaly-detection.md)
* [Receita: Explorador de Arte](./recipes/art-explorer.md)