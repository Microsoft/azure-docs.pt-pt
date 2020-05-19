---
title: 'Quickstart: Ligue para o seu ponto final de pesquisa personalizada bing usando Python / Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para começar a solicitar resultados de pesquisa da sua instância de pesquisa personalizada Bing usando Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: e1248a4e90653f0e862841ec6f58a12364943590
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196563"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Quickstart: Ligue para o seu ponto final de pesquisa personalizada bing usando Python

Utilize este quickstart para aprender a solicitar resultados de pesquisa a partir da sua instância de Pesquisa Personalizada Bing. Embora esta aplicação esteja escrita em Python, o Bing Custom Search API é um serviço web RESTful compatível com a maioria dos idiomas de programação. O código fonte desta amostra está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de pesquisa personalizada bing. Para mais informações, consulte [Quickstart: Crie a sua primeira instância](quick-start.md)de pesquisa personalizada bing .
- [Pitão](https://www.python.org/) 2.x ou 3.x.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

- Crie um novo ficheiro Python no seu IDE ou editor favorito e adicione as seguintes declarações de importação. Crie variáveis para a sua chave de subscrição, ID de configuração personalizada e termo de pesquisa.

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber um pedido de pesquisa 

1. Construa o URL de pedido, afinando o seu termo de pesquisa ao parâmetro de consulta e o ID de configuração personalizado da sua instância de `q=` pesquisa para o `customconfig=` parâmetro. Separe os parâmetros com uma ampersand ( `&` ). Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final de [subdomínio personalizado](../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Envie o pedido para a sua instância de Pesquisa Personalizada Bing e imprima os resultados de pesquisa devolvidos.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa uma aplicação web de pesquisa personalizada](./tutorials/custom-search-web-page.md)
