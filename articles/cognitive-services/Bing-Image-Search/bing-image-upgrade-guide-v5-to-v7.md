---
title: Upgrade de Bing Image Search API v5 para v7
titleSuffix: Azure Cognitive Services
description: Este guia de atualização descreve alterações entre a versão 5 e a versão 7 da API de Pesquisa de Imagem Bing. Utilize este guia para o ajudar a identificar as partes da sua aplicação que precisa de atualizar para utilizar a versão 7.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68883489"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Guia de upgrade da API v7 de pesquisa de imagem bing

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de Pesquisa de Imagem Bing. Utilize este guia para o ajudar a identificar as partes da sua aplicação que precisa de atualizar para utilizar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- O número da versão do ponto final mudou de v5 para v7. Por exemplo,\/https:\*\*/api.cognitive.microsoft.com/bing/ v7.0**/images/search.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta a erros e códigos de erro

- Todos os pedidos falhados `ErrorResponse` devem agora incluir um objeto no corpo de resposta.

- Adicione os seguintes `Error` campos ao objeto.  
  - `subCode`&mdash;Divisórias o código de erro em baldes discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre `message` o erro descrito no campo


- Substituiu os códigos de erro `code` `subCode` v5 com os seguintes valores possíveis e valores.

|Código|Subcódigo|Descrição
|-|-|-
|Error do servidor|Erro Inesperado<br/>Erro de Recursos<br/>Não Implementado|Bing devolve ServerError sempre que ocorrer em qualquer uma das condições do subcódigo. A resposta inclui estes erros se o código de estado HTTP for de 500.
|Pedido inválido|Parâmetros desaparecidos<br/>ParâmetroSInvalidValue<br/>Httpnotado<br/>Bloqueado|Devoluções Bing InvalidRequest sempre que qualquer parte do pedido não for válida. Por exemplo, falta um parâmetro necessário ou não é válido um valor de parâmetro.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de estado HTTP é de 400.<br/><br/>Se o erro for httpNotAllowed, o código de estado HTTP 410.
|RateLimitExceeded||Bing devolve RateLimitExceeded sempre que excede as suas consultas por segundo (QPS) ou consultas por mês (QPM) quota.<br/><br/>Bing devolve o código de estado HTTP 429 se exceder qPS e 403 se exceder o QPM.
|Autorização Inválida|Autorização Faltando<br/>AutorizaçãoRedundy|Bing devolve InvalidAuthorization quando Bing não pode autenticar o chamador. Por exemplo, `Ocp-Apim-Subscription-Key` falta o cabeçalho ou a chave de subscrição não é válida.<br/><br/>O despedimento ocorre se especificar mais do que um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de estado HTTP é 401.
|Autorização Insuficiente|AutorizaçãoDeficiente<br/>Autorizações Caducadas|Bing devolve InsuficienteAutorização quando o chamador não tem permissões para aceder ao recurso. Isto pode ocorrer se a chave de subscrição tiver sido desativada ou tiver expirado. <br/><br/>Se o erro for insuficiente, o código de estado HTTP é 403.

- Os seguintes mapas dos códigos de erro anteriores para os novos códigos. Se tiver tomado uma dependência dos códigos de erro V5, atualize o seu código em conformidade.

|Código versão 5|Versão 7 código.subCode
|-|-
|RequestParameter Missing|Pedido inválido.Parâmetro desaparecido
RequestParameterInvalidValue|Pedido inválido.parameterInvalidValue
Acesso de RecursosNegado|Autorização Insuficiente
Volume Excedido|RateLimitExceeded
UltrapassadoQpsLimit|RateLimitExceeded
Desativado|Autorização Insuficiente.AutorizaçãoDeficiente
Erro Inesperado|Error server.UnexpectedError
Erros dataSource|Error de servidor.Error de recursos
Autorização Faltando|Autorização Inválida.Autorização Em Falta
Httpnotado|Pedido Inválido.httpNotAllowed
Agente de utilizador Desaparecido|Pedido inválido.Parâmetro desaparecido
Não Implementado|Error server.NotImplemented
Autorização Inválida|Autorização Inválida
Método de Autorização Inválida|Autorização Inválida
Método de Autorização Múltipla|Autorização Inválida.AutorizaçãoDesem Despedimento
Caducada caducidade|Autorização Insuficiente.Autorizações Caducadas
InsuficienteScope|Autorização Insuficiente
Bloqueado|Pedido inválido.Bloqueado



### <a name="query-parameters"></a>Parâmetros de consulta

- Renomeado `modulesRequested` o parâmetro de consulta para [módulos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Rebatizou as Anotações para Tags. Consulte o parâmetro de consulta de [módulos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) para Tags.  

- Alterou a lista de mercados suportados do valor do filtro Das Fontes comerciais apenas para en-US. Ver [imagemType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Mudanças de insights de imagem

- Renomeado `annotations` o campo de `imageTags` [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) para .  

- Renomeado `AnnotationModule` o objeto para [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- Rebatizou `Annotation` o objeto de `confidence` [Tag](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag)e retirou o campo.  

- Renomeado `insightsSourcesSummary` o campo do `insightsMetadata`objeto de [imagem](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) para .  

- Renomeado `InsightsSourcesSummary` o objeto para [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- Acrescentou `https://api.cognitive.microsoft.com/bing/v7.0/images/details` o ponto final. Utilize este ponto final para solicitar insights de imagem em vez do ponto final /imagens/pesquisa. Ver [Insights de Imagem](./image-insights.md).

- Os seguintes parâmetros de consulta `/images/details` são agora válidos apenas com o ponto final.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [módulos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [táxi](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [gato](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [ct](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Renomeado `ImageInsightsResponse` o objeto para [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- Alterou os tipos de dados dos seguintes campos no objeto [ImageInsights.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights)  

    -   Alterou o `relatedCollections` tipo `ImageGallery[]` de campo de para [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Alterou o `pagesIncluding` tipo `Image[]` de campo de [imagens Módulo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Alterou o `relatedSearches` tipo `Query[]` de campo de distância para [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Alterou o `recipes` tipo `Recipe[]` de campo de [receitas Módulo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Alterou o `visuallySimilarImages` tipo `Image[]` de campo de [imagens Módulo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Alterou o `visuallySimilarProducts` tipo `ProductSummaryImage[]` de campo de [imagens Módulo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Removeu `ProductSummaryImage` o objeto e moveu os campos relacionados com o produto para o objeto [Image.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) O `Image` objeto inclui os campos relacionados com o produto apenas quando a imagem é incluída como parte de produtos visualmente semelhantes numa resposta de insight de imagem.  

    -   Alterou o `recognizedEntityGroups` tipo `RecognizedEntityGroup[]` de campo para [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Rebatizou `categoryClassification` o campo `annotations`de [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) para `AnnotationsModule`, e mudou o seu tipo para .  

### <a name="images-answer"></a>Resposta das imagens

-   Removeu os campos de visualizaçãoShoppingSourcesBadges e displayRecipeSourcesBadges from [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   Renomeado `nextOffsetAddCount` o campo `nextOffset`de [Imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) para . A forma como usas a compensação também mudou. Anteriormente, define [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) o parâmetro de consulta `nextOffsetAddCount` offset para o valor mais o valor de compensação anterior mais o número de imagens no resultado. Agora, estás `offset` `nextOffset` no valor.  


## <a name="non-breaking-changes"></a>Alterações não-destantes

### <a name="query-parameters"></a>Parâmetros de consulta

- Adicionado Transparente como um possível valor de filtro [de imagemType.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) O filtro transparente devolve apenas imagens com um fundo transparente.

- Acrescentou o Any como um possível valor de filtro de [licença.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) O filtro Qualquer filtro devolve apenas imagens que estão sob licença.

- Adicione os parâmetros de consulta [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) e [minFileSize.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) Utilize estes filtros para devolver imagens dentro de uma gama de tamanhos de ficheiros.  

- Adicione os parâmetros de consulta [maxHeight,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight) [minHeight,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight) [maxWidth,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth) [minWidth.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) Utilize estes filtros para devolver imagens dentro de uma gama de alturas e larguras.  

### <a name="object-changes"></a>Alterações de objetos

- Adicione `description` os `lastUpdated` campos e campos ao objeto [oferta.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer)  

- Adicionei `name` o campo ao objeto [da ImageGallery.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery)  

- Adicionado `similarTerms` ao objeto [Imagens.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) Este campo contém uma lista de termos semelhantes em significado à cadeia de consulta do utilizador.  
