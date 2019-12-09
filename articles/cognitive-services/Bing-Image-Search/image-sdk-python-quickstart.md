---
title: 'Início rápido: Pesquisar por imagens-SDK de Pesquisa de Imagem do Bing para Python'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de início rápido para criar a sua primeira pesquisa de imagens com o SDK de Pesquisa de Imagens do Bing, um wrapper para a API com as mesmas funcionalidades. Esta aplicação Python simples envia uma consulta de pesquisa de imagens, analisa a resposta JSON e apresenta o URL da primeira imagem devolvida.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 85aa1bda395240d0f11b0654ee48b9f1a0401eaa
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930600"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-python"></a>Guia de Início Rápido: Procurar imagens com o SDK de Pesquisa de Imagens do Bing para Python

Utilize este guia de início rápido para criar a sua primeira pesquisa de imagens com o SDK de Pesquisa de Imagens do Bing, um wrapper para a API com as mesmas funcionalidades. Esta aplicação Python simples envia uma consulta de pesquisa de imagens, analisa a resposta JSON e apresenta o URL da primeira imagem devolvida.

O código fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) com processamento de erros e anotações de código adicionais.

## <a name="prerequisites"></a>Pré-requisitos

* [Python 2.7 ou 3.4](https://www.python.org/) e superior.

* O [SDK de Pesquisa de Imagens do Azure](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) para Python
    * Instalar com `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo script Python no seu IDE ou editor favorito e as seguintes importações:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Crie variáveis para a sua chave de subscrição e termo de pesquisa.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Criar o cliente de pesquisa de imagens

1. Crie uma instância do `CognitiveServicesCredentials` e utilize-a para instanciar o cliente:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
1. Envie uma consulta de pesquisa para a API de Pesquisa de Imagens do Bing:
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>Processar e ver os resultados

Analise os resultados da imagem devolvidos na resposta.


Se a resposta contiver os resultados da pesquisa, armazene o primeiro resultado e imprima os detalhes, como um URL de miniatura, o URL original, juntamente com o número total de imagens devolvidas.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Bing Image Search single-page app tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) (Tutorial de aplicação de página única da Pesquisa de Imagens do Bing)

## <a name="see-also"></a>Ver também

* [O que é a Pesquisa de Imagens do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Experimentar uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obter uma chave de acesso aos Serviços Cognitivos gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Exemplos de Python para o SDK dos Serviços Cognitivos do Azure](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Documentação dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)
