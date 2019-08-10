---
title: Atualizar do API de Pesquisa de Imagem do Bing V5 para v7
titleSuffix: Azure Cognitive Services
description: Este guia de atualização descreve as alterações entre a versão 5 e a versão 7 do API de Pesquisa de Imagem do Bing. Use este guia para ajudá-lo a identificar as partes do seu aplicativo que você precisa atualizar para usar a versão 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: c4c6b95996206cfb38ea3f77b89c3ebe3c2c0026
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883489"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Guia de atualização do API de Pesquisa de Imagem do Bing v7

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 do API de Pesquisa de Imagem do Bing. Use este guia para ajudá-lo a identificar as partes do seu aplicativo que você precisa atualizar para usar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- O número de versão do ponto de extremidade foi alterado de V5 para v7. Por exemplo, https:\//API.cognitive.Microsoft.com/Bing/\*\*v 7.0 * */images/Search.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta de erro e códigos de erro

- Todas as solicitações com falha agora devem `ErrorResponse` incluir um objeto no corpo da resposta.

- Foram adicionados os campos a seguir `Error` ao objeto.  
  - `subCode`&mdash;Particiona o código de erro em buckets discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrito no `message` campo


- Substituídos os códigos de erro V5 pelos seguintes `code` valores `subCode` e possíveis.

|Código|Subcódigo|Descrição
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|O Bing retorna ServerError sempre que qualquer uma das condições de subcódigo ocorrerem. A resposta incluirá esses erros se o código de status HTTP for 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing retorna InvalidRequest sempre que qualquer parte da solicitação não é válida. Por exemplo, um parâmetro obrigatório está ausente ou um valor de parâmetro não é válido.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de status HTTP será 400.<br/><br/>Se o erro for HttpNotAllowed, o código de status HTTP 410.
|RateLimitExceeded||O Bing retorna RateLimitExceeded sempre que você excede a cota de consultas por segundo (QPS) ou consultas por mês (QPM).<br/><br/>O Bing retornará o código de status HTTP 429 se você excedeu QPS e 403 se excedeu QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing retorna InvalidAuthorization quando o Bing não pode autenticar o chamador. Por exemplo, o `Ocp-Apim-Subscription-Key` cabeçalho está ausente ou a chave de assinatura não é válida.<br/><br/>A redundância ocorrerá se você especificar mais de um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de status HTTP será 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing retorna InsufficientAuthorization quando o chamador não tem permissões para acessar o recurso. Isso pode ocorrer se a chave de assinatura tiver sido desabilitada ou tiver expirado. <br/><br/>Se o erro for InsufficientAuthorization, o código de status HTTP será 403.

- O seguinte mapeia os códigos de erro anteriores para os novos códigos. Se você tiver adotado uma dependência de códigos de erro V5, atualize seu código de acordo.

|Código da versão 5|Código da versão 7. subcódigo
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Desativado|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Bloqueado|InvalidRequest. bloqueado



### <a name="query-parameters"></a>Parâmetros de consulta

- Parâmetro de `modulesRequested` consulta renomeado para [módulos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- As anotações foram renomeadas para marcas. Consulte parâmetros de consulta de [módulos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) para marcas.  

- Alteração da lista de mercados com suporte do valor de filtro ShoppingSources para en-US apenas. Consulte [ImageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Alterações de imagem insights

- Renomeou o `annotations` campo de [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) para `imageTags`.  

- O `AnnotationModule` objeto foi renomeado para [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- O `Annotation` objeto foi renomeado para [marcação](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag)e o `confidence` campo foi removido.  

- O `insightsSourcesSummary` campo do objeto de [imagem](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) foi renomeado `insightsMetadata`para.  

- O `InsightsSourcesSummary` objeto foi renomeado para [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- Adicionado o `https://api.cognitive.microsoft.com/bing/v7.0/images/details` ponto de extremidade. Use esse ponto de extremidade para solicitar informações de imagem em vez do ponto de extremidade/images/Search. Consulte insights de [imagem](./image-insights.md).

- Os parâmetros de consulta a seguir agora são válidos somente `/images/details` com o ponto de extremidade.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [módulos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [ct](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- O `ImageInsightsResponse` objeto foi renomeado para [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- Foram alterados os tipos de dados dos campos a seguir no objeto [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) .  

    -   Alterou o tipo do `relatedCollections` campo de `ImageGallery[]` para [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Alterou o tipo do `pagesIncluding` campo de `Image[]` para [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Alterou o tipo do `relatedSearches` campo de `Query[]` para [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Alterou o tipo do `recipes` campo de `Recipe[]` para [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Alterou o tipo do `visuallySimilarImages` campo de `Image[]` para [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Alterou o tipo do `visuallySimilarProducts` campo de `ProductSummaryImage[]` para [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Removeu `ProductSummaryImage` o objeto e moveu os campos relacionados ao produto para o objeto [Image](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) . O `Image` objeto inclui os campos relacionados ao produto somente quando a imagem é incluída como parte dos produtos visualmente semelhantes em uma resposta de insight de imagem.  

    -   Alterou o tipo do `recognizedEntityGroups` campo de `RecognizedEntityGroup[]` para [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Renomeou o `categoryClassification` campo de [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) para `annotations`e alterou seu tipo para `AnnotationsModule`.  

### <a name="images-answer"></a>Resposta de imagens

-   Removidos os campos displayShoppingSourcesBadges e displayRecipeSourcesBadges das [imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   O `nextOffsetAddCount` campo de [imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) foi renomeado `nextOffset`para. A maneira como você usa o deslocamento também foi alterada. Anteriormente, você definiu [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) o parâmetro de consulta offset `nextOffsetAddCount` para o valor mais o valor de deslocamento anterior mais o número de imagens no resultado. Agora, você define `offset` para o `nextOffset` valor.  


## <a name="non-breaking-changes"></a>Alterações não significativas

### <a name="query-parameters"></a>Parâmetros de consulta

- Adicionado transparente como um valor [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) de filtro ImageType possível. O filtro transparente retorna apenas imagens com um plano de fundo transparente.

- Adicionado o qualquer como um valor de filtro de [licença](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) possível. O filtro qualquer retorna apenas imagens que estão sob licença.

- Os parâmetros de consulta [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) e [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) foram adicionados. Use esses filtros para retornar imagens dentro de um intervalo de tamanhos de arquivo.  

- Foram adicionados os parâmetros de consulta [MaxHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [MinHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [MaxWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth), [minWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) . Use esses filtros para retornar imagens dentro de um intervalo de alturas e larguras.  

### <a name="object-changes"></a>Alterações de objeto

- Adicionados os `description` campos `lastUpdated` e ao objeto de [oferta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) .  

- Adicionado o `name` campo ao objeto [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery) .  

- Adicionado `similarTerms` ao objeto [images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) . Este campo contém uma lista de termos que são semelhantes em significado à cadeia de caracteres de consulta do usuário.  
