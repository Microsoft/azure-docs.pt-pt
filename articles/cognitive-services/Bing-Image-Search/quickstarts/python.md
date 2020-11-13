---
title: 'Quickstart: Procure imagens usando a API e Python de pesquisa de imagem Bing'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para enviar pedidos de pesquisa de imagem para a API de pesquisa de imagem Bing usando Python, e receba respostas JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 0c7494f62f181e34e4879f1e64bf2c4ee1cf52ec
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593438"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Quickstart: Procure imagens usando a API e Python de pesquisa de imagem Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Utilize este quickstart para aprender a enviar pedidos de pesquisa para a API de Pesquisa de Imagem Bing. Esta aplicação Python envia uma consulta de pesquisa para a API, e apresenta o URL da primeira imagem nos resultados. Embora esta aplicação esteja escrita em Python, a API é um serviço web RESTful compatível com a maioria das linguagens de programação.

Para executar este exemplo como um caderno Jupyter no [MyBinder,](https://mybinder.org)selecione o crachá **de lançamento:**

[![Encadernação](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


O código fonte deste exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) com processamento de erros e anotações de código adicionais.


## <a name="prerequisites"></a>Pré-requisitos

* [Python 2.x ou 3.x](https://www.python.org/)
* A [Biblioteca de Imagens Python (PIL)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro Python no seu IDE ou editor favorito e importe os seguintes módulos. Crie uma variável para a sua chave de subscrição, ponto final de pesquisa e termo de pesquisa. Para `search_url` , pode utilizar o ponto final global no seguinte código, ou utilizar o ponto final [de subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Adicione a sua chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho criando um dicionário e adicionando a chave como um valor. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Criar e enviar um pedido de pesquisa

1. Crie um dicionário para os parâmetros do pedido de pesquisa. Adicione o seu termo de pesquisa ao `q` parâmetro. Desa estale o `license` parâmetro `public` para procurar imagens no domínio público. Desema esta procura `imageType` `photo` apenas por fotos.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Utilize a `requests` biblioteca para ligar para a API de Pesquisa de Imagem Bing. Adicione o seu cabeçalho e parâmetros ao pedido e devolva a resposta como um objeto JSON. Obtenha os URLs em várias imagens de miniaturas do campo de `thumbnailUrl` resposta.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
    ```

## <a name="view-the-response"></a>Ver a resposta

1. Crie uma nova figura com quatro colunas e quatro linhas utilizando a biblioteca matplotlib. 

2. Iterar através das linhas e colunas da figura, e usar o método da biblioteca PIL `Image.open()` para adicionar uma miniatura de imagem a cada espaço. 

3. Utilize `plt.show()` para desenhar a figura e exibir as imagens.

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    plt.show()
    ```


## <a name="example-json-response"></a>Exemplo JSON resposta

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Bing Image Search single-page app tutorial](../tutorial-bing-image-search-single-page-app.md) (Tutorial de aplicação de página única da Pesquisa de Imagens do Bing)

* [O que é a API de Pesquisa de Imagens do Bing?](../overview.md)  
* [Detalhes dos preços das APIs de pesquisa de Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Documentação dos Serviços Cognitivos da Azure](../../index.yml)
* [Bing Image Search API reference](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)