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
ms.date: 05/12/2020
ms.author: aahi
ms.openlocfilehash: 3a90d5455c0664ceabf80647fc94a37ad0c716b5
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873029"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Quickstart: Envie uma consulta à API local de pesquisa de negócios bing em Python

Use este quickstart para aprender a enviar pedidos para a API de Pesquisa De Negócios Local Bing, que é um Serviço Cognitivo Azure. Embora esta aplicação simples esteja escrita em Python, a API é um serviço Web RESTful compatível com qualquer linguagem de programação capaz de fazer pedidos HTTP e analisar jSON.

Esta aplicação de exemplo obtém dados de resposta local da API para uma consulta de pesquisa.

## <a name="prerequisites"></a>Pré-requisitos

* [Pitão](https://www.python.org/) 2.x ou 3.x.
* Uma [conta de API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com APIs de Pesquisa bing. Para este início rápido, o [julgamento gratuito](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente. Guarde a chave API fornecida quando ativar o seu teste gratuito. Para mais informações, consulte [Preços dos Serviços Cognitivos - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Executar a aplicação completa

O exemplo seguinte obtém resultados localizados, que são implementados nos seguintes passos:
1. Declare variáveis para especificar o ponto final por anfitrião e por caminho.
2. Especifique o parâmetro de consulta. 
3. Defina a função de pesquisa que cria o pedido e adiciona o `Ocp-Apim-Subscription-Key` cabeçalho.
4. Desloque o `Ocp-Apim-Subscription-Key` cabeceamento. 
5. Faça a ligação e envie o pedido.
6. Imprima os resultados JSON.

O código completo para esta demonstração é o seguinte:

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
- [Pesquisa de negócios locais Java quickstart](local-search-java-quickstart.md)
- [Pesquisa de negócios local C# quickstart](local-quickstart.md)
- [Local Business Search Node.js quickstart](local-search-node-quickstart.md)
