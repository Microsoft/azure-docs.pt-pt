---
title: Quickstart - Envie uma consulta para a API em Python - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Use este quickstart para começar a usar a API de pesquisa de negócios local Bing em Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: tracking-python
ms.openlocfilehash: fcd3ab3cce74aa0ef021427904077c364de1f493
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84606280"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Quickstart: Envie uma consulta para a API de Pesquisa de Negócios Locais de Bing em Python

Use este quickstart para aprender a enviar pedidos para a Bing Local Business Search API, que é um Serviço Cognitivo Azure. Embora esta aplicação simples esteja escrita em Python, a API é um serviço Web RESTful compatível com qualquer linguagem de programação capaz de fazer pedidos HTTP e analisar json.

Este exemplo de aplicação obtém dados de resposta local da API para uma consulta de pesquisa.

## <a name="prerequisites"></a>Pré-requisitos

* [Pitão](https://www.python.org/) 2.x ou 3.x.
* Uma [conta API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com APIs de Pesquisa de Bing. Para este arranque rápido, o [julgamento livre](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente. Guarde a chave API fornecida quando ativar o seu teste gratuito. Para mais informações, consulte [preços dos serviços cognitivos - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Executar a aplicação completa

O exemplo a seguir obtém resultados localizados, que são implementados nos seguintes passos:
1. Declare variáveis para especificar o ponto final por anfitrião e por caminho.
2. Especifique o parâmetro de consulta. 
3. Defina a função de pesquisa que cria o pedido e adiciona o `Ocp-Apim-Subscription-Key` cabeçalho.
4. Coloque o `Ocp-Apim-Subscription-Key` cabeçalho. 
5. Faça a ligação e envie o pedido.
6. Imprima os resultados JSON.

O código completo desta demonstração é o seguinte:

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

## <a name="next-steps"></a>Próximos passos
- [Pesquisa de negócios locais Java quickstart](local-search-java-quickstart.md)
- [Pesquisa de negócios locais C# quickstart](local-quickstart.md)
- [Node.js quickstart de pesquisa de negócios locais](local-search-node-quickstart.md)
