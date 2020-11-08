---
title: Serviços cognitivos para amostras de Big Data Scala
description: Utilize serviços cognitivos para a Azure Databricks para executar o seu pipeline MMLSpark para grandes dados.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: c47aa803774343b39efeabe3452f1b256cc64c0d
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363277"
---
# <a name="quick-examples"></a>Exemplos Rápidos

Os seguintes cortes estão prontos para correr e ajudarão a começar a usar os Serviços Cognitivos em Spark. As amostras abaixo estão em Scala.

As amostras utilizam estes Serviços Cognitivos:

- Text Analytics - obtenha o sentimento (ou humor) de um conjunto de frases.
- Computer Vision - obtenha as etiquetas (descrições de uma palavra) associadas a um conjunto de imagens.
- Bing Image Search - procure na web imagens relacionadas com uma consulta de linguagem natural.
- Speech-to-text - transcreva ficheiros áudio para extrair transcrições baseadas em texto.
- Detetor de Anomalias - detete anomalias dentro de dados de séries de tempo.

## <a name="prerequisites"></a>Pré-requisitos

1. Siga os passos em [Começar](getting-started.md) a configurar o seu ambiente Azure Databricks e Serviços Cognitivos. Este tutorial incluirá como instalar o MMLSpark e como criar o seu cluster Spark em Databricks.
1. Depois de criar um novo caderno em Azure Databricks, copie o **código Partilhado** abaixo e cole-o numa nova célula no seu caderno.
1. Escolha uma amostra de serviço, abaixo, e copie-a para uma segunda célula nova no seu caderno.
1. Substitua qualquer um dos espaços reservados para a subscrição do serviço pela sua própria chave.
1. Escolha o botão de funcionação (ícone triângulo) no canto superior direito da célula e, em seguida, selecione **Run Cell**.
1. Ver resultados numa tabela abaixo da célula.

## <a name="shared-code"></a>Código compartilhado
Para começar, adicione este código ao seu projeto:

```scala
import com.microsoft.ml.spark.cognitive._
import spark.implicits._ 

val serviceKey = "ADD-YOUR-SUBSCRIPTION-KEY"
val location = "eastus"
```

## <a name="text-analytics"></a>Análise de Texto

O serviço [Text Analytics](../text-analytics/index.yml) fornece vários algoritmos para extrair insights inteligentes do texto. Por exemplo, podemos encontrar o sentimento de texto de entrada dado. O serviço devolverá uma pontuação entre `0.0` e `1.0` onde as pontuações baixas indicam sentimento negativo e pontuação elevada indica um sentimento positivo.  A amostra abaixo usa três frases simples e devolve a pontuação de sentimento para cada um.

```scala
import org.apache.spark.sql.functions.col

val df = Seq(
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US")
).toDF("text", "language")

val sentiment = new TextSentiment()
    .setTextCol("text")
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language")

display(sentiment.transform(df).select(col("text"), col("sentiment")(0).getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>Resultado esperado

| texto                                      | sentimento                                             |
|:------------------------------------------|:------------------------------------------------------|
| Estou tão feliz hoje, está ensolarado!           | 0.9789592027664185                                    |
| Estou frustrado com este tráfego de hora de ponta | 0.023795604705810547                                  |
| Os serviços cognitivos na faísca aint mau  | 0.8888956308364868                                    |

## <a name="computer-vision"></a>Imagem Digitalizada

[A Visão Computacional](../computer-vision/index.yml) analisa imagens para identificar estruturas como rostos, objetos e descrições de linguagem natural.
Nesta amostra, marcamos uma lista de imagens. Etiquetas são descrições de uma palavra das coisas na imagem como objetos reconhecíveis, pessoas, cenários e ações.

```scala
// Create a dataframe with the image URLs
val df = Seq(
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg")
).toDF("image")

// Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
val analysis = new AnalyzeImage()
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setVisualFeatures(Seq("Categories","Color","Description","Faces","Objects","Tags"))
    .setOutputCol("results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

// Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select(col("image"), col("results").getItem("tags").getItem("name")).alias("results")))

// Uncomment for full results with all visual feature requests
//display(analysis.transform(df).select(col("image"), col("results")))
``` 

### <a name="expected-result"></a>Resultado esperado

| image | etiquetas |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ['skate' 'homem' 'homem' 'outdoor' 'riding' 'sport' 'skate' 'young' 'board' 'air' 'air' 'black' 'boy' 'side' 'jumping' 'trick' 'ramp' 'doing' 'flying']
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ['dog' 'outdoor' 'vedação' 'madeira' 'pequeno' 'brown' 'sentando's 'frente' 'banco' 'pé' 'mesa' 'tábua' 'board' 'praia' 'branco' 'segurando' 'ponte' 'pista']                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ['outdoor' 'erva' 'casa' 'edifício' 'casa antiga' 'casa' 'frente' 'pequena' 'igreja' 'pedra' 'grande' 'pastagem' 'quintal' 'verde' 'sentado' 'liderando' 'ovelha' 'tijolo» 'banco' 'rua' 'branco' 'relógio' 'relógio' 'sinal' 'estacionado' 'campo' 'jardim' 'água' 'cavalo' 'cavalo' 'alto' 'fogo' 'grupo'] 

## <a name="bing-image-search"></a>Pesquisa de Imagens do Bing

[Bing Image Search](../bing-image-search/overview.md) procura na web para recuperar imagens relacionadas com a consulta de linguagem natural de um utilizador. Nesta amostra, usamos uma consulta de texto que procura imagens com citações. Devolve uma lista de URLs de imagem que contêm fotos relacionadas com a nossa consulta.


```scala
import org.apache.spark.ml.Pipeline

// Number of images Bing will return per query
val imgsPerBatch = 10

// A list of offsets, used to page into the search results
val df = (0 until 100).map(o => Tuple1(o*imgsPerBatch)).toSeq.toDF("offset")

// Run the Bing Image Search service with our text query
val bingSearch = new BingImageSearch()
    .setSubscriptionKey(bingSearchKey)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images")

// Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
val getUrls = BingImageSearch.getUrlTransformer("images", "url")

// This displays the full results returned, uncomment to use
// display(bingSearch.transform(bingParameters))

// Since we have two services, they are put into a pipeline
val pipeline = new Pipeline().setStages(Array(bingSearch, getUrls))

// Show the results of your search: image URLs
display(pipeline.fit(df).transform(df))
```

### <a name="expected-result"></a>Resultado esperado

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |

## <a name="speech-to-text"></a>Conversão de Voz em Texto

O [serviço Discurso-a-Texto](../speech-service/index-speech-to-text.yml) converte streams ou ficheiros de áudio falado para texto. Nesta amostra, transcrevemos dois ficheiros áudio. O primeiro ficheiro é fácil de entender, e o segundo é mais desafiante.

```scala
import org.apache.spark.sql.functions.col

// Create a dataframe with audio URLs, tied to the column called "url"
val df = Seq(("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav"),
             ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3")).toDF("url")

// Run the Speech-to-text service to translate the audio into text
val speechToText = new SpeechToTextSDK()
    .setSubscriptionKey(serviceKey)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked")

// Show the results of the translation
display(speechToText.transform(df).select(col("url"), col("text").getItem("DisplayText")))
```

### <a name="expected-result"></a>Resultado esperado

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | A fala personalizada fornece ferramentas que permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando os dados áudio com o resultado correspondente do reconhecimento do portal de fala personalizado. Pode reproduzir áudio carregado e determinar se o resultado do reconhecimento fornecido está correto. Esta ferramenta permite-lhe inspecionar rapidamente a qualidade do discurso de base da Microsoft para o modelo de texto ou um modelo personalizado treinado sem ter de transcrever quaisquer dados áudio. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Adicione um cavalheiro, Senhor, a pensar em verificação visual.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Estou a ouvir-me. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Gosto da garantia de que também posso ouvi-la. |

## <a name="anomaly-detector"></a>Detetor de Anomalias

[O Detetor de Anomalias](../anomaly-detector/index.yml) é ótimo para detetar irregularidades nos dados da série de tempo. Nesta amostra, usamos o serviço para encontrar anomalias em toda a série de tempo.

```scala
import org.apache.spark.sql.functions.{col, lit}

val anomalyKey = "84a2c303cc7e49f6a44d692c27fb9967"

val df = Seq(
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
  ).toDF("timestamp", "value").withColumn("group", lit("series1"))

// Run the Anomaly Detector service to look for irregular data
val anamolyDetector = new SimpleDetectAnomalies()
    .setSubscriptionKey(anomalyKey)
    .setLocation("eastus")
    .setTimestampCol("timestamp")
    .setValueCol("value")
    .setOutputCol("anomalies")
    .setGroupbyCol("group")
    .setGranularity("monthly")

// Show the full results of the analysis with the anomalies marked as "True"
display(anamolyDetector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
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