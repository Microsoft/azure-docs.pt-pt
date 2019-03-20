---
title: Atualização do Bing imagens a API de pesquisa v5 para v7
titleSuffix: Azure Cognitive Services
description: Este guia de atualização descreve as alterações entre a versão 5 e a versão 7 da API de pesquisa de imagens do Bing. Utilize este guia para ajudar a identificar as partes da sua aplicação que tem de atualizar para utilizar a versão 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 123c5556dc76b35cf4a6b4b34e0c3e2fe437cebe
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996453"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Guia de atualização de API de pesquisa de imagens do Bing v7

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de pesquisa de imagens do Bing. Utilize este guia para ajudar a identificar as partes da sua aplicação que tem de atualizar para utilizar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- Número da versão do ponto final foi alterado de v5 para v7. Por exemplo, https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta de erro e códigos de erro

- Todos os pedidos falhados agora devem incluir um `ErrorResponse` objeto no corpo da resposta.

- Adicionados os seguintes campos para o `Error` objeto.  
  - `subCode`&mdash;O código de erro de partições em grupos discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrita a `message` campo


- Substituído os códigos de erro v5 com os seguintes possíveis `code` e `subCode` valores.

|Código|SubCode|Descrição
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NãoImplementado|O Bing devolve ServerError sempre que qualquer uma das condições código secundárias ocorrer. A resposta inclui estes erros se o código de estado HTTP é 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing devolve InvalidRequest sempre que qualquer parte do pedido não é válido. Por exemplo, um parâmetro necessário está em falta ou um valor de parâmetro não é válido.<br/><br/>Se o erro é ParameterMissing ou ParameterInvalidValue, o código de estado HTTP é 400.<br/><br/>Se o erro é HttpNotAllowed, o código de estado HTTP 410.
|RateLimitExceeded||O Bing devolve RateLimitExceeded sempre que excedem suas consultas por segundo (QPS) ou consultas por quota do mês (QPM).<br/><br/>O Bing devolve o código de estado HTTP 429 se excedido QPS e 403 se excedido QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing devolve InvalidAuthorization ao Bing não é possível autenticar o chamador. Por exemplo, o `Ocp-Apim-Subscription-Key` cabeçalho está em falta ou a chave de subscrição não é válida.<br/><br/>Redundância ocorre se especificar mais do que um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de estado HTTP é 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing devolve InsufficientAuthorization quando o chamador não tem permissões para aceder ao recurso. Isto pode ocorrer se a chave de subscrição foi desativada ou expirou. <br/><br/>Se o erro for InsufficientAuthorization, o código de estado HTTP é 403.

- O seguinte mapeia os códigos de erro anteriores para os códigos de novo. Se obtém uma dependência sobre códigos de erros de v5, atualize o seu código em conformidade.

|Código da versão 5|Versão 7 code.subCode
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
NãoImplementado|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Bloqueado|InvalidRequest.Blocked



### <a name="query-parameters"></a>Parâmetros de consulta

- Mudar o nome da `modulesRequested` parâmetro de consulta [módulos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).  

- Mudar o nome as anotações para etiquetas. Ver [módulos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) parâmetro às marcas de consulta.  

- Alterar a lista de mercados suportados do valor de filtro ShoppingSources para en-US apenas. Ver [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Alterações de informações de imagem

- Mudar o nome da `annotations` campo [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) para `imageTags`.  

- Mudar o nome da `AnnotationModule` objeto para [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule).  

- Mudar o nome a `Annotation` objeto para [marca](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag)e removido o `confidence` campo.  

- Mudar o nome da `insightsSourcesSummary` campo a [imagem](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) de objeto para `insightsMetadata`.  

- Mudar o nome da `InsightsSourcesSummary` objeto para [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata).  

- Adicionado o `https://api.cognitive.microsoft.com/bing/v7.0/images/details` ponto final. Utilize este ponto final para as informações de imagem do pedido em vez do / imagens/ponto final da pesquisa. Ver [informações da imagem](./image-insights.md).

- Os seguintes parâmetros de consulta agora são válidos apenas com o `/images/details` ponto final.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [Módulos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [ct](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  

- Mudar o nome da `ImageInsightsResponse` objeto para [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  

- Alterar os tipos de dados dos campos seguintes no [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights) objeto.  

    -   Alterar o tipo dos `relatedCollections` campo `ImageGallery[]` para [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Alterar o tipo dos `pagesIncluding` campo `Image[]` para [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Alterar o tipo dos `relatedSearches` campo `Query[]` para [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Alterar o tipo dos `recipes` campo `Recipe[]` para [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule).  

    -   Alterar o tipo dos `visuallySimilarImages` campo `Image[]` para [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Alterar o tipo dos `visuallySimilarProducts` campo `ProductSummaryImage[]` para [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Removida a `ProductSummaryImage` de objeto e movê-la os campos relacionados com o produto para o [imagem](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objeto. O `Image` objeto inclui os campos relacionados com o produto, apenas quando a imagem é incluída como parte dos produtos visualmente semelhantes numa resposta de informações de imagem.  

    -   Alterar o tipo dos `recognizedEntityGroups` campo `RecognizedEntityGroup[]` para [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Mudar o nome da `categoryClassification` campo [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) para `annotations`e alterar o tipo como `AnnotationsModule`.  

### <a name="images-answer"></a>Resposta de imagens

-   Remover os campos displayShoppingSourcesBadges e displayRecipeSourcesBadges da [imagens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images).  

-   Mudar o nome da `nextOffsetAddCount` campo [imagens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) para `nextOffset`. A forma como utiliza o deslocamento também foi alterado. Anteriormente, definir o [desvio](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) parâmetro para consulta o `nextOffsetAddCount` valor mais o valor de deslocamento anterior mais o número de imagens no resultado. Agora, defina `offset` para o `nextOffset` valor.  


## <a name="non-breaking-changes"></a>Alterações sem interrupções

### <a name="query-parameters"></a>Parâmetros de consulta

- Adicionado Transparent como uma possível [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) valor do filtro. O filtro transparente devolve apenas as imagens com fundo transparente.

- Adicionado quaisquer uma possível [licença](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) valor do filtro. O filtro de qualquer devolve apenas as imagens que estão sob a licença.

- Adicionado o [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) e [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) parâmetros de consulta. Utilize estes filtros de retornar imagens dentro de um intervalo de tamanhos de ficheiros.  

- Adicionado o [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth), [minWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth) parâmetros de consulta. Utilize estes filtros de retornar imagens dentro de um intervalo de alturas e larguras.  

### <a name="object-changes"></a>Alterações de objetos

- Adicionado o `description` e `lastUpdated` campos para o [oferecer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer) objeto.  

- Adicionado o `name` campo para o [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery) objeto.  

- Adicionado `similarTerms` para o [imagens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objeto. Este campo contém uma lista de termos que são semelhantes em significado à cadeia de consulta do utilizador.  
