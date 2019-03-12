---
title: Início rápido - enviar uma consulta para o Local Business API de pesquisa Bing em Python | Documentos da Microsoft
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para começar a utilizar a API de pesquisa de negócios locais do Bing em Python.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 241ade7d4866186946c371aa1786bc913d045483
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762947"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Início rápido: Enviar uma consulta para o Local Business API de pesquisa Bing em Python

Utilize este guia de introdução para começar a enviar pedidos para o Local Business API de pesquisa Bing, que é um serviço cognitivos do Azure. Embora esse simples aplicativo é escrito em Python, a API é um serviço RESTful Web compatível com qualquer linguagem de programação capaz de fazer pedidos de HTTP e analisar JSON.

Esta aplicação de exemplo obtém os dados do local de resposta da API para a consulta de pesquisa `hotel in Bellevue`.

## <a name="prerequisites"></a>Pré-requisitos

* [Python](https://www.python.org/) 2.x ou 3.x
 
Tem de ter uma [conta de API dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com as APIs do Bing. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente para este guia de início rápido. Utilize a chave de acesso fornecida pela versão de avaliação gratuita.  Consulte também [dos serviços cognitivos preços - API de pesquisa Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Executar a aplicação concluída

O código seguinte obtém resultados localizados. É implementado nos passos seguintes:
1. Declare variáveis para especificar o ponto final por anfitrião e por caminho.
2. Especifique o parâmetro de consulta. 
3. Defina a função de pesquisa que cria o pedido e adiciona o cabeçalho de Ocp-Apim-Subscription-Key.
4. Defina o cabeçalho de Ocp-Apim-Subscription-Key. 
5. Fazer a conexão e enviar o pedido.
6. Imprima os resultados JSON.

Segue-se o código completo para esta demonstração:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com/bing'
path = '/v7.0/localbusinesses/search'

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

## <a name="next-steps"></a>Passos Seguintes
- [Início rápido de Java de pesquisa comercial](local-search-java-quickstart.md)
- [Pesquisa de negócios locais C# início rápido](local-quickstart.md)
- [Início rápido do nó de pesquisa comercial](local-search-node-quickstart.md)
