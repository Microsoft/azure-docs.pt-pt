---
title: 'Início rápido: Procure imagens - API de REST de pesquisa de imagens do Bing e Python'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de introdução para enviar pedidos de pesquisa de imagem para a API de pesquisa de imagens do Bing REST com o Python e receber respostas JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 0fa60f8dc7a1bb0f72080e91adb1149c1c4c082d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60913807"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Início rápido: Procure imagens usando a API de REST de pesquisa de imagens do Bing e Python

Utilize este guia de introdução para começar a enviar pedidos de pesquisa para a API de pesquisa de imagens do Bing. Esta aplicação de Python envia uma consulta de pesquisa para a API e apresenta o URL da imagem do primeiro nos resultados. Embora esse aplicativo é escrito em Python, a API é um serviço RESTful web compatível com a maioria das linguagens de programação.

Pode executar este exemplo como um bloco de notas do Jupyter no [MyBinder](https://mybinder.org), ao clicar no destaque de lançamento do Binder:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


O código fonte deste exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) com processamento de erros e anotações de código adicionais.


## <a name="prerequisites"></a>Pré-requisitos

* [Python 2.x ou 3.x](https://www.python.org/)
* O [Python biblioteca (PIL) de geração de imagens](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Criar um novo ficheiro de Python no seu IDE ou editor favorito e importe os seguintes módulos. Criar uma variável para a sua chave de subscrição, procure o ponto final e termo de pesquisa.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho ao criar um dicionário e adicionar a chave como um valor. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Criar e enviar um pedido de pesquisa

1. Crie um dicionário para a pesquisa parâmetros do pedido. Adicionar o termo de pesquisa para o `q` parâmetro. Usar "public" para o `license` parâmetro para procurar imagens de domínio público. Utilize o "de fotos" para o `imageType` de pesquisa apenas de fotos.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Utilize o `requests` biblioteca para chamar a API de pesquisa de imagens do Bing. Adicionar o cabeçalho e parâmetros para a solicitação e retornar a resposta como um objeto JSON. 

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

## <a name="view-the-response"></a>Ver a resposta

1. Crie uma nova figura com quatro colunas e linhas de quatro usando a biblioteca de matplotlib. 

2. Iterar por meio da figura linhas e colunas e utilize a biblioteca de PIL `Image.open()` método para adicionar uma miniatura de imagem a cada espaço. 

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    ```

3. Utilize `plt.show()` para desenhar a figura e apresentar as imagens.

## <a name="example-json-response"></a>Resposta JSON de exemplo

As respostas da API de Pesquisa de Imagens do Bing são devolvidas como JSON. Esta resposta de amostra foi truncada para mostrar um único resultado.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Bing Image Search single-page app tutorial](../tutorial-bing-image-search-single-page-app.md) (Tutorial de aplicação de página única da Pesquisa de Imagens do Bing)

* [O que é a API de pesquisa de imagens do Bing?](../overview.md)  
* [Os detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) para as APIs de pesquisa do Bing. 
* [Obter uma chave de acesso aos Serviços Cognitivos gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Documentação dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)
