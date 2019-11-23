---
title: Quickstart - Send a query to the API in Python - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Use this article to start using the Bing Local Business Search API in Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/06/2019
ms.author: aahi
ms.openlocfilehash: da8a0a7c27294838f8f5c4459eda890c1cbbec7a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326675"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Quickstart: Send a query to the Bing Local Business Search API in Python

Use this quickstart to begin sending requests to the Bing Local Business Search API, which is an Azure Cognitive Service. While this simple application is written in Python, the API is a RESTful Web service compatible with any programming language capable of making HTTP requests and parsing JSON.

This example application gets local response data from the API for the search query `hotel in Bellevue`.

## <a name="prerequisites"></a>Pré-requisitos

* [Python](https://www.python.org/) 2.x or 3.x
 
You must have a [Cognitive Services API account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) with Bing APIs. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente para este guia de início rápido. Use the access key provided by the free trial.  See also [Cognitive Services Pricing - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Run the complete application

The following code gets localized results. É implementado nos passos seguintes:
1. Declare variáveis para especificar o ponto final por anfitrião e por caminho.
2. Specify the query parameter. 
3. Define the Search function that creates the request and adds the Ocp-Apim-Subscription-Key header.
4. Set the Ocp-Apim-Subscription-Key header. 
5. Make the connection and send the request.
6. Imprima os resultados JSON.

Segue-se o código completo para esta demonstração:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Passos seguintes
- [Local Business Search Java Quickstart](local-search-java-quickstart.md)
- [Local Business Search C# Quickstart](local-quickstart.md)
- [Local Business Search Node Quickstart](local-search-node-quickstart.md)
