---
title: 'Receita: Exploração de Arte Inteligente com os Serviços Cognitivos para Big Data'
titleSuffix: Azure Cognitive Services
description: Esta receita mostra como criar uma base de dados de arte pesquisável usando a Azure Search e a MMLSpark.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 0a94c66eb51298db226ceec5da5c86666576052a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850496"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>Receita: Exploração de Arte Inteligente com os Serviços Cognitivos para Big Data

Neste exemplo, vamos usar os Serviços Cognitivos para Big Data para adicionar anotações inteligentes à coleção Open Access do Metropolitan Museum of Art (MET). Isto permitir-nos-á criar um motor de busca inteligente utilizando a Azure Search mesmo sem anotações manuais. 

## <a name="prerequisites"></a>Pré-requisitos

* Deve ter uma chave de subscrição para visão de computador e pesquisa cognitiva. Siga as instruções na [Conta Criar serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever a Visão de Computador e obter a sua chave.
  > [!NOTE]
  > Para obter informações sobre preços, consulte [a Azure Cognitive Search](https://azure.microsoft.com/services/search/#pricing).

## <a name="import-libraries"></a>Bibliotecas de Importação

Executar o seguinte comando para importar bibliotecas para esta receita.

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>Configurar chaves de subscrição

Executar o seguinte comando para configurar variáveis para as teclas de serviço. Insira as suas teclas de subscrição para Visão de Computador e Pesquisa Cognitiva Azure.

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>Ler os Dados

Executar o seguinte comando para carregar os dados da coleção de acesso aberto do MET.

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>Analisar as Imagens

Executar o seguinte comando para utilizar a Visão de Computador na coleção de obras de arte de acesso aberto do MET. Como resultado, você terá características visuais das obras de arte.

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>Criar o Índice de Pesquisa

Executar o seguinte comando para escrever os resultados para a Azure Search para criar um motor de busca das obras de arte com metadados enriquecidos da Visão de Computador.

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>Consulta do Índice de Pesquisa

Executar o seguinte comando para consultar o índice de pesquisa Azure.

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar [serviços cognitivos para big data para deteção de anomalias.](anomaly-detection.md)

