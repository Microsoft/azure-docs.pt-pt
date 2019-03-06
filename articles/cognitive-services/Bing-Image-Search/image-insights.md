---
title: Obtenha informações de imagem - API de pesquisa de imagens do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar a API de pesquisa de imagens do Bing para obter mais informações sobre uma imagem.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: 33dfbe70d75335eca79d32807407b97e32c2dbbf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440867"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Obtenha informações de imagem com a API de pesquisa de imagens do Bing

> [!IMPORTANT]
> Em vez de utilizar o endpoint/imagens/detalhes para obter informações de imagem, deve usar [pesquisa Visual](../bing-visual-search/overview.md) , uma vez que ele fornece informações mais abrangentes.


Cada imagem inclui um token de informações que pode utilizar para obter informações sobre a imagem. Por exemplo, pode obter uma coleção de imagens relacionadas, páginas da web que incluem a imagem, ou uma lista de comerciantes onde podem comprar o produto mostrado na imagem.  

Para obter informações sobre uma imagem, capturar a imagem [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken) token na resposta.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

Em seguida, chamar o ponto de extremidade de detalhes de imagem e definir o [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parâmetro para o token na consulta `imageInsightsToken`.  

Para especificar as informações que deseja obter, definir o `modules` parâmetro de consulta. Para obter todas as informações, defina `modules` para `All`. Para obter apenas as informações de legenda e uma coleção, defina `modules` para `Caption%2CCollection`. Para obter uma lista completa de informações possíveis, consulte [módulos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested). Nem todas as informações estão disponíveis para todas as imagens. A resposta inclui todas as informações que pediu, se disponível.

O exemplo seguinte solicita todas as informações disponíveis para a imagem anterior.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>A obter informações de uma imagem de conhecidos

Se tiver o URL para uma imagem que pretende obter informações de, utilize o [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) parâmetro em vez de consulta do [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parâmetro para especificar a imagem. Em alternativa, se tiver o ficheiro de imagem, pode enviar o binário da imagem no corpo de um pedido POST. Se utilizar um pedido POST, o `Content-Type` cabeçalho deve ser definido como `multipart/data-form`. Com qualquer uma das opções, o tamanho da imagem não pode exceder 1 MB.  

Se tiver um URL para a imagem, o exemplo seguinte mostra como pedir informações de uma imagem.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Obter todas as informações de imagem  

Para todas as informações de uma imagem do pedido, defina o [módulos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parâmetro para consulta `All`. Para obter pesquisas relacionadas, o pedido tem de incluir cadeia de consulta do utilizador. Este exemplo mostra a utilizar o [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) para especificar a imagem.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

O objeto de nível superior é um [ImageInsightsResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) objeto em vez de um [imagens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objeto.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>Reconhecimento de entidades numa imagem  

O recurso de reconhecimento de entidades identifica as entidades numa imagem, atualmente apenas as pessoas. Para identificar as entidades numa imagem, defina o [módulos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parâmetro para consulta `RecognizedEntities`.  

> [!NOTE]
> Não pode especificar este módulo com qualquer outro módulo. Se especificar este módulo com outros módulos, a resposta não inclui entidades reconhecidas.  

O seguinte mostra como especificar a imagem com o [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) parâmetro. Não se esqueça de URL codificar os parâmetros de consulta.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

O código a seguir mostra a resposta ao pedido anterior. Como a imagem contém duas pessoas, a resposta identifica uma região para cada pessoa. Neste caso, as pessoas foram reconhecidas nos grupos CelebrityAnnotations e CelebRecognitionAnnotations. Bing apresenta uma lista de pessoas em cada grupo com base na probabilidade que correspondem a pessoa da imagem original. A lista está por ordem descendente de confiança. O grupo de CelebRecognitionAnnotations fornece o maior nível de confiança de que a correspondência está correta.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

O `region` campo identifica a área da imagem onde Bing reconhecido a entidade. Para pessoas, a região representa face da pessoa.  

Os valores do retângulo são relativas a largura e altura da imagem original e o intervalo de 0,0 através de 1.0. Por exemplo, se a imagem é 300 x 200 e top da região, canto esquerdo no ponto (10, 20) e na parte inferior, canto direito está no ponto (290, 150), então o retângulo normalizado é:  

-   À esquerda: 10 / 300 = 0.03333...  
-   Parte superior:  20 / 200 = 0.1  
-   Direita: 290 / 300 = 0.9667...  
-   Na parte inferior: 150 / 200 = 0.75  

Pode utilizar a região que o Bing devolve nas chamadas subsequentes insights. Por exemplo, para obter imagens visualmente similares da entidade reconhecida. Para obter mais informações, consulte corte imagens a utilizar com módulos de reconhecimento de entidades e visualmente semelhantes. O código a seguir mostra o mapeamento entre os campos de região e os parâmetros de consulta que usaria para recortar imagens.  

-   À esquerda é mapeado para [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
-   Principais mapeados para [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
-   Direita é mapeado para [carro](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
-   Inferior é mapeado para [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Localizar imagens visualmente similares  

Para localizar imagens visualmente semelhantes à imagem original, defina o [módulos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parâmetro para SimilarImages de consulta.  

O pedido seguinte mostra como obter imagens visualmente similares. O pedido utiliza a [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parâmetro para identificar a imagem original de consulta. Para melhorar a relevância, deve incluir a cadeia de consulta do utilizador.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


O código a seguir mostra a resposta ao pedido anterior.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Corte imagens a utilizar com visualmente semelhantes e módulos de reconhecimento de entidades  

Para especificar a região da imagem que o Bing utiliza para determinar se as imagens são visualmente semelhantes ou para realizar o reconhecimento de entidades, utilize o [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal), [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat), [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)e [carro](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car) parâmetros de consulta. Por predefinição, o Bing utiliza a imagem inteira.  

Os parâmetros especificam a parte superior, esquerdo canto e na parte inferior, canto direito da região que o Bing utiliza para comparação. Especifique os valores como frações dos largura e altura da imagem original. Os valores fracionários começam (0,0, 0,0) no início, no canto esquerdo e no fim com (1.0, 1.0) no canto inferior direito. Por exemplo, para especificar que o canto superior, esquerdo percorre um trimestre do caminho da parte superior e um quarto da forma em do lado esquerdo, defina `cal` para 0,25 e `cat` 0,25.  

A seguinte sequência de chamadas mostra o efeito de especificar a região de recorte. A primeira chamada não inclui corte e Bing reconhece duas pessoas pé lado a lado no meio da imagem.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

A resposta mostra duas entidades reconhecidas.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

A segunda chamada recorta a imagem na vertical pela metade e Bing reconhecido uma única pessoa no lado direito da imagem.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

A resposta mostra uma entidade reconhecida.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>Encontrar produtos visualmente semelhantes  

Para localizar imagens que contêm produtos visualmente semelhantes aos produtos encontrados na imagem original, defina o [módulos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parâmetro para SimilarProducts de consulta.  

O pedido seguinte mostra como obter imagens dos produtos visualmente semelhantes. O pedido utiliza a [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) consulta parâmetro para identificar o original de imagem que foi devolvida num pedido anterior. Para melhorar a relevância, deve incluir a cadeia de consulta do utilizador.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

O código a seguir mostra a resposta ao pedido anterior. A resposta contém uma imagem de um produto semelhante e indica quantos comerciantes oferecem o produto online, se há classificações de produto e o preço mais baixo encontrado (consulte a `aggregateOffer` campo).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

Para obter uma lista dos comerciantes que oferecem o produto online (consulte a [offerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) campo), chamar a API novamente e defina `modules` para ShoppingSources. Em seguida, defina o `insightsToken` parâmetro de consulta para o token encontrado na imagem de resumo do produto.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Segue-se a resposta à solicitação anterior.  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```
