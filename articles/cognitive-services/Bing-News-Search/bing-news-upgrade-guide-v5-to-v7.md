---
title: Atualizar a API de pesquisa de notícias do Bing v5 para v7
titlesuffix: Azure Cognitive Services
description: Identifica as partes da sua aplicação que tem de atualizar para utilizar a versão 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 235cc1c74c099a71d289d38369ebc10132564825
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66383301"
---
# <a name="news-search-api-upgrade-guide"></a>Guia de atualização de API de pesquisa de notícias

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de pesquisa de notícias do Bing. Utilize este guia para ajudar a identificar as partes da sua aplicação que tem de atualizar para utilizar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- Número da versão do ponto final foi alterado de v5 para v7. Por exemplo, https://api.cognitive.microsoft.com/bing/ **v7.0**  /notícias/search.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta de erro e códigos de erro

- Todos os pedidos falhados agora devem incluir um `ErrorResponse` objeto no corpo da resposta.

- Adicionados os seguintes campos para o `Error` objeto.  
  - `subCode`&mdash;O código de erro de partições em grupos discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrita a `message` campo

- Substituído os códigos de erro v5 com os seguintes possíveis `code` e `subCode` valores.

|Código|SubCode|Descrição
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|O Bing devolve ServerError sempre que qualquer uma das condições código secundárias ocorrer. A resposta inclui estes erros se o código de estado HTTP é 500.
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
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Bloqueado|InvalidRequest.Blocked

### <a name="object-changes"></a>Alterações de objetos

- Adicionado o `contractualRules` campo para o [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) objeto. O `contractualRules` campo contém uma lista de regras que tem de seguir (por exemplo, a atribuição do artigo). Tem de aplicar a atribuição fornecida `contractualRules` em vez de usar `provider`. O artigo inclui `contractualRules` apenas quando o [API de pesquisa Web](../bing-web-search/search-the-web.md) resposta contém uma resposta de notícias.

## <a name="non-breaking-changes"></a>Alterações sem interrupções

### <a name="query-parameters"></a>Parâmetros de consulta

- Produtos foi adicionado como um valor possível que pode definir o [categoria](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) parâmetro de consulta. Ver [categorias por mercados](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

- Adicionado o [SortBy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) parâmetro de consulta, que devolve os tópicos em destaque ordenados por data com a mais recente primeiro.

- Adicionado o [uma vez que](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) parâmetro de consulta, que devolve os tópicos em destaque que foram detetados pelo Bing ou após o período de tempo de "Epoch" especificado do Unix.

### <a name="object-changes"></a>Alterações de objetos

- Adicionado o `mentions` campo para o [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) objeto. O `mentions` campo contém uma lista de entidades (pessoas ou lugares) que foram encontradas no artigo.

- Adicionado o `video` campo para o [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) objeto. O `video` campo contém um vídeo que está relacionado com o artigo de notícias. O vídeo é um \<iframe\> que pode incorporar ou uma miniatura de movimento.

- Adicionado o `sort` campo para o [notícias](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) objeto. O `sort` campo mostra a ordem de classificação dos artigos. Por exemplo, os artigos são ordenados por relevância (predefinição) ou data.

- Adicionado o [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) objeto, que define uma ordem de classificação. O `isSelected` campo indica se a resposta utilizado a ordem de classificação. Se **true**, a resposta usado a ordem de classificação. Se `isSelected` é **false**, pode utilizar o URL no `url` campo para pedir uma ordem de classificação diferente.
