---
title: Atualizar API de Pesquisa de Notícias do Bing V5 para v7
titleSuffix: Azure Cognitive Services
description: Identifica as partes do seu aplicativo que você precisa atualizar para usar a versão 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 1263e93b1e316cab4afb51cd828737a5bd087fed
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423838"
---
# <a name="news-search-api-upgrade-guide"></a>Guia de atualização da API Pesquisa de Notícias

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 do API de Pesquisa de Notícias do Bing. Use este guia para ajudá-lo a identificar as partes do seu aplicativo que você precisa atualizar para usar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- O número de versão do ponto de extremidade foi alterado de V5 para v7. Por exemplo, https://api.cognitive.microsoft.com/bing/ **v 7.0**/News/Search.

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

### <a name="object-changes"></a>Alterações de objeto

- Adicionado o `contractualRules` campo ao objeto [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) . O `contractualRules` campo contém uma lista de regras que você deve seguir (por exemplo, atribuição de artigo). Você deve aplicar a atribuição fornecida em `contractualRules` em vez de usar `provider`o. O artigo inclui `contractualRules` apenas quando a resposta da [API pesquisa na Web](../bing-web-search/search-the-web.md) contém uma resposta de notícias.

## <a name="non-breaking-changes"></a>Alterações não significativas

### <a name="query-parameters"></a>Parâmetros de consulta

- Produtos adicionados como um valor possível para o qual você pode definir o parâmetro de consulta de [categoria](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) . Consulte [categorias por mercados](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

- Adicionado o parâmetro de consulta [sortBy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) , que retorna tópicos de tendência classificados por data com o mais recente primeiro.

- Adicionado o parâmetro de consulta [Since](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) , que retorna tópicos de tendência que foram descobertos pelo Bing no ou após o carimbo de data/hora de época UNIX especificado.

### <a name="object-changes"></a>Alterações de objeto

- Adicionado o `mentions` campo ao objeto [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) . O `mentions` campo contém uma lista de entidades (pessoas ou lugares) que foram encontradas no artigo.

- Adicionado o `video` campo ao objeto [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) . O `video` campo contém um vídeo relacionado ao artigo de notícias. O vídeo é um \<iframe\> que você pode inserir ou uma miniatura de movimento.

- Adicionado o `sort` campo ao objeto [News](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) . O `sort` campo mostra a ordem de classificação dos artigos. Por exemplo, os artigos são classificados por relevância (padrão) ou data.

- Adicionado o [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) objeto sortvalue, que define uma ordem de classificação. O `isSelected` campo indica se a resposta usou a ordem de classificação. Se **for true**, a resposta usou a ordem de classificação. Se `isSelected` for **false**, você poderá usar `url` a URL no campo para solicitar uma ordem de classificação diferente.
