---
title: 'Início rápido: chamar seu ponto de extremidade de Pesquisa Personalizada do Bing usando Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para começar a solicitar os resultados da pesquisa de sua instância de Pesquisa Personalizada do Bing usando o Python
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: cbfc122913978b1e0828917e901422942928644d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974449"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Início rápido: chamar seu ponto de extremidade de Pesquisa Personalizada do Bing usando Python

Use este guia de início rápido para começar a solicitar resultados de pesquisa de sua instância de Pesquisa Personalizada do Bing. Embora esse aplicativo seja escrito em Python, o API de Pesquisa Personalizada do Bing é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de Pesquisa Personalizada do Bing. Consulte [início rápido: criar sua primeira instância de pesquisa personalizada do Bing](quick-start.md) para obter mais informações.
- [Python](https://www.python.org/) 2. x ou 3. x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo arquivo Python em seu IDE ou editor favorito e adicione as seguintes instruções de importação. Crie variáveis para sua chave de assinatura, ID de configuração personalizada e um termo de pesquisa. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber uma solicitação de pesquisa 

1. Construa a URL de solicitação acrescentando seu termo de pesquisa ao parâmetro de consulta `q=` e a ID de configuração personalizada da sua instância de pesquisa para `customconfig=`. Separe os parâmetros com um caractere de `&`. Você pode usar o ponto de extremidade global abaixo ou o ponto de extremidade de [subdomínio personalizado](../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Envie a solicitação para sua instância de Pesquisa Personalizada do Bing e imprima os resultados de pesquisa retornados.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de pesquisa personalizado](./tutorials/custom-search-web-page.md)
