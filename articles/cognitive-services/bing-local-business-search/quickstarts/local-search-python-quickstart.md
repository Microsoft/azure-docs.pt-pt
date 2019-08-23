---
title: Início rápido – enviar uma consulta para a API de pesquisa de negócios local do Bing no Python
titleSuffix: Azure Cognitive Services
description: Use este artigo para começar a usar a API de pesquisa de negócios local do Bing no Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 100d731614e2193c5f858249315f748836323b4c
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906259"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Início rápido: Enviar uma consulta para a API de pesquisa de negócios local do Bing no Python

Use este guia de início rápido para começar a enviar solicitações para a API de pesquisa de negócios local do Bing, que é um serviço de cognitiva do Azure. Embora esse aplicativo simples seja escrito em Python, a API é um serviço Web RESTful compatível com qualquer linguagem de programação capaz de fazer solicitações HTTP e analisar JSON.

Este aplicativo de exemplo obtém dados de resposta locais da API para a consulta `hotel in Bellevue`de pesquisa.

## <a name="prerequisites"></a>Pré-requisitos

* [Python](https://www.python.org/) 2. x ou 3. x
 
Você deve ter uma [conta de API de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com APIs do Bing. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente para este guia de início rápido. Use a chave de acesso fornecida pela avaliação gratuita.  Consulte também [dos serviços cognitivos preços - API de pesquisa Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Executar o aplicativo completo

O código a seguir obtém os resultados localizados. É implementado nos passos seguintes:
1. Declare variáveis para especificar o ponto final por anfitrião e por caminho.
2. Especifique o parâmetro de consulta. 
3. Defina a função de pesquisa que cria a solicitação e adiciona o cabeçalho OCP-APIM-Subscription-Key.
4. Defina o cabeçalho OCP-APIM-Subscription-Key. 
5. Faça a conexão e envie a solicitação.
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

## <a name="next-steps"></a>Passos seguintes
- [Início rápido do Java da pesquisa de negócios local](local-search-java-quickstart.md)
- [Início rápido de C# pesquisa comercial local](local-quickstart.md)
- [Início rápido do nó de pesquisa comercial local](local-search-node-quickstart.md)
