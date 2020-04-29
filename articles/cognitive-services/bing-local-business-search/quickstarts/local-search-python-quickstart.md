---
title: Quickstart - Envie uma consulta à API em Python - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Use este quickstart para começar a usar a API de Pesquisa de Negócios Local Bing em Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: c7e7ef7f052fccfea18b246f41109d5fa7528b4b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75379748"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Quickstart: Envie uma consulta à API local de pesquisa de negócios bing em Python

Use este quickstart para começar a enviar pedidos para a API local de pesquisa de negócios Bing, que é um Serviço Cognitivo Azure. Embora esta aplicação simples esteja escrita em Python, a API é um serviço Web RESTful compatível com qualquer linguagem de programação capaz de fazer pedidos HTTP e analisar a JSON.

Esta aplicação de exemplo obtém dados de `hotel in Bellevue`resposta local da API para a consulta de pesquisa .

## <a name="prerequisites"></a>Pré-requisitos

* [Pitão](https://www.python.org/) 2.x ou 3.x
 
Deve ter uma conta de API de [Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com APIs bing. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente para este início rápido. Utilize a chave de acesso fornecida pelo teste gratuito.  Consulte também [preços de serviços cognitivos - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Executar a aplicação completa

O código seguinte obtém resultados localizados. É implementado nos passos seguintes:
1. Declare variáveis para especificar o ponto final por anfitrião e por caminho.
2. Especifique o parâmetro de consulta. 
3. Defina a função Pesquisa que cria o pedido e adiciona o cabeçalho Ocp-Apim-Subscription-Key.
4. Defina o cabeçalho Ocp-Apim-Subscription-Key. 
5. Faça a ligação e envie o pedido.
6. Imprima os resultados JSON.

Segue-se o código completo para esta demonstração:

```python
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
- [Pesquisa de negócios locais Java Quickstart](local-search-java-quickstart.md)
- [Pesquisa de negócios local C# Quickstart](local-quickstart.md)
- [Nó de pesquisa de negócios local Quickstart](local-search-node-quickstart.md)
