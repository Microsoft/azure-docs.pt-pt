---
title: 'Início rápido: Chamar seu ponto de extremidade de Pesquisa Personalizada do Bing usando Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para começar a solicitar os resultados da pesquisa de sua instância de Pesquisa Personalizada do Bing usando o Python
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 7d3aeb2fd9072c508cad5adec9ce9129087d3a88
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405137"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Início rápido: Chamar seu ponto de extremidade de Pesquisa Personalizada do Bing usando Python

Use este guia de início rápido para começar a solicitar resultados de pesquisa de sua instância de Pesquisa Personalizada do Bing. Embora esse aplicativo seja escrito em Python, o API de Pesquisa Personalizada do Bing é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de Pesquisa Personalizada do Bing. Consulte [início rápido: Crie sua primeira instância](quick-start.md) de pesquisa personalizada do Bing para obter mais informações.
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

1. Construa a URL de solicitação acrescentando seu termo de pesquisa ao `q=` parâmetro de consulta e a ID de configuração personalizada da sua instância `customconfig=`de pesquisa para. Separe os parâmetros com um `&` caractere. 

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Envie a solicitação para sua instância de Pesquisa Personalizada do Bing e imprima os resultados de pesquisa retornados.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de pesquisa personalizado](./tutorials/custom-search-web-page.md)
