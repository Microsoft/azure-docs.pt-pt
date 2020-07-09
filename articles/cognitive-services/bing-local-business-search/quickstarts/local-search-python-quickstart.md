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
ms.openlocfilehash: c821df0e7cb00c73899a2694dd0b2eb6823b1d9e
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85611201"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Quickstart: Envie uma consulta para a API de Pesquisa de Negócios Locais de Bing em Python

Use este quickstart para aprender a enviar pedidos para a Bing Local Business Search API, que é um Serviço Cognitivo Azure. Embora esta aplicação simples esteja escrita em Python, a API é um serviço Web RESTful compatível com qualquer linguagem de programação capaz de fazer pedidos HTTP e analisar json.

Este exemplo de aplicação obtém dados de resposta local da API para uma consulta de pesquisa.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Pitão](https://www.python.org/) 2.x ou 3.x.
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" Crie um recurso Bing Search crie um "  target="_blank"> recurso Bing Search no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.

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

## <a name="next-steps"></a>Passos seguintes
- [Pesquisa de negócios locais Java quickstart](local-search-java-quickstart.md)
- [Pesquisa de negócios locais C# quickstart](local-quickstart.md)
- [Pesquisa de negócios local Node.js arranque rápido](local-search-node-quickstart.md)
