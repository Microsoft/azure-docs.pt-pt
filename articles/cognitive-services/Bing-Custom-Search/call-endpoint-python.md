---
title: 'Quickstart: Ligue para o seu ponto final de pesquisa personalizada Bing usando Python / Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para começar a solicitar resultados de pesquisa a partir do seu caso de Pesquisa Personalizada Bing usando Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: b214eaebf10473985ef5458590d962d37797c4e4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080996"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Quickstart: Ligue para o seu ponto final de pesquisa personalizada Bing usando Python

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Utilize este quickstart para aprender a solicitar resultados de pesquisa a partir da sua instância de Pesquisa Personalizada Bing. Embora esta aplicação esteja escrita em Python, a Bing Custom Search API é um serviço web RESTful compatível com a maioria das linguagens de programação. O código-fonte desta amostra está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Pré-requisitos

- Um caso de pesquisa personalizada Bing. Para obter mais informações, consulte [Quickstart: Crie a sua primeira instância de Pesquisa Personalizada Bing](quick-start.md).
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

1. Construa o URL de pedido, anexando o seu termo de pesquisa ao `q=` parâmetro de consulta, e o ID de configuração personalizado da sua instância de pesquisa para o `customconfig=` parâmetro. Separe os parâmetros com uma ampersand ( `&` ). Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [de subdomínio personalizado](../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

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
> [Construa um aplicativo web de pesquisa personalizada](./tutorials/custom-search-web-page.md)
