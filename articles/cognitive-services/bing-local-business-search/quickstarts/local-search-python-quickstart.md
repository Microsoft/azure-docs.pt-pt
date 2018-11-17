---
title: Início rápido - enviar uma consulta para o Local Business API de pesquisa Bing em Python | Documentos da Microsoft
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para começar a utilizar a API de pesquisa de negócios locais do Bing em Python.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: ccac5f986b765e03caf939e28c5bcb7757e729b6
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852304"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Início rápido: Enviar uma consulta para o Local Business API de pesquisa Bing em Python

Utilize este guia de introdução para começar a enviar pedidos para o Local Business API de pesquisa Bing, que é um serviço cognitivos do Azure. Embora esse simples aplicativo é escrito em Python, a API é um serviço RESTful Web compatível com qualquer linguagem de programação capaz de fazer pedidos de HTTP e analisar JSON.

Esta aplicação de exemplo obtém os dados do local de resposta da API para a consulta de pesquisa `hotel in Bellevue`.

## <a name="prerequisites"></a>Pré-requisitos

* [Python](https://www.python.org/) 2.x ou 3.x
 
Tem de ter uma [conta de API dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com as APIs do Bing. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente para este guia de início rápido. Utilize a chave de acesso fornecida pela versão de avaliação gratuita.

## <a name="run-the-complete-application"></a>Executar a aplicação concluída

O código seguinte obtém resultados localizados. É implementado nos passos seguintes:
1. Declare variáveis para especificar o ponto final por anfitrião e por caminho.
2. Especifique o parâmetro de consulta. 
3. Defina a função de pesquisa que cria o pedido e adiciona o cabeçalho de Ocp-Apim-Subscription-Key.
4. Defina o cabeçalho de Ocp-Apim-Subscription-Key. 
5. Fazer a conexão e enviar o pedido.
6. Imprima os resultados JSON.

Segue-se o código completo para esta demonstração:

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com/bing'
path = '/v7.0/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&appid=' + subscriptionKey + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

````

## <a name="next-steps"></a>Passos Seguintes
- [Início rápido de Java de pesquisa comercial](local-search-java-quickstart.md)
- [Pesquisa de negócios locais C# início rápido](local-quickstart.md)
- [Início rápido do nó de pesquisa comercial](local-search-node-quickstart.md)