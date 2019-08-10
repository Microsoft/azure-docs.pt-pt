---
title: Atualização da API V5 para o v7-API de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: Determine quais partes do seu aplicativo exigem atualizações para usar as APIs do Pesquisa na Web do Bing v7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 2133cd59c524112ae8a77c0a20cbce1d1336a38d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881311"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Atualizar do API de Pesquisa na Web do Bing V5 para v7

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 do API de Pesquisa na Web do Bing. Use este guia para ajudá-lo a identificar as partes do seu aplicativo que você precisa atualizar para usar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- O número de versão do ponto de extremidade foi alterado de V5 para v7. Por exemplo, https:\/\/API.cognitive.Microsoft.com/Bing/**v 7.0**/Search.

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
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing retorna InsufficientAuthorization quando o chamador não tem permissões para acessar o recurso. Esse erro poderá ocorrer se a chave de assinatura tiver sido desabilitada ou tiver expirado. <br/><br/>Se o erro for InsufficientAuthorization, o código de status HTTP será 403.

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


## <a name="non-breaking-changes"></a>Alterações não significativas  

### <a name="headers"></a>Cabeçalhos

- O cabeçalho de solicitação [pragma](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) opcional foi adicionado. Por predefinição, o Bing devolve conteúdo em cache, se disponível. Para impedir que o Bing devolva conteúdo em cache, defina o cabeçalho Pragma como no-cache (por exemplo, Pragma: no-cache).

### <a name="query-parameters"></a>Parâmetros de consulta

- Adicionado o parâmetro de consulta [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) . Use esse parâmetro para especificar o número de respostas que você deseja que a resposta inclua. As respostas são escolhidas com base na classificação. Por exemplo, se você definir esse parâmetro como três (3), a resposta incluirá as três principais respostas classificadas.  

- Adicionado o [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) parâmetro de consulta promote. Use esse parâmetro junto com `answerCount` para incluir explicitamente um ou mais tipos de resposta, independentemente de sua classificação. Por exemplo, para promover vídeos e imagens na resposta, você definirá promover para *vídeos, imagens*. A lista de respostas que você deseja promover não conta o `answerCount` limite. Por exemplo, se `answerCount` for 2 e `promote` for definido como *vídeos, imagens*, a resposta poderá incluir páginas da Web, notícias, vídeos e imagens.

### <a name="object-changes"></a>Alterações de objeto

- O `someResultsRemoved` campo foi adicionado ao [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) objeto webanswer. O campo contém um valor booliano que indica se a resposta excluiu alguns resultados da resposta da Web.  
