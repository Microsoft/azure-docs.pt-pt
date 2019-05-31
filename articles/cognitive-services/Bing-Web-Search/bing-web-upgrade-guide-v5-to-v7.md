---
title: Atualizar do API v5 para v7 - API de pesquisa Web do Bing
titleSuffix: Azure Cognitive Services
description: Determine que partes do seu exigirá de aplicação das atualizações ao utilizar APIs de pesquisa Web Bing v7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: reference
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: e3d78a1b7488e7489b02e34e9733a5d741213855
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384897"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Atualização do Bing Web API de pesquisa v5 para v7

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de pesquisa Web do Bing. Utilize este guia para ajudar a identificar as partes da sua aplicação que tem de atualizar para utilizar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- Número de versão do ponto final foi alterado de v5 para v7. Por exemplo, https:\/\/api.cognitive.microsoft.com/bing/**v7.0**  /de pesquisa.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta de erro e códigos de erro

- Todos os pedidos falhados agora devem incluir um `ErrorResponse` objeto no corpo da resposta.

- Adicionados os seguintes campos para o `Error` objeto.  
  - `subCode`&mdash;O código de erro de partições em grupos discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrita a `message` campo


- Substituído os códigos de erro v5 com os seguintes possíveis `code` e `subCode` valores.

|Código|SubCode|Descrição
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|O Bing devolve ServerError sempre que qualquer uma das condições código secundárias ocorrer. A resposta irá incluir estes erros se o código de estado HTTP é 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing devolve InvalidRequest sempre que qualquer parte do pedido não é válido. Por exemplo, um parâmetro necessário está em falta ou um valor de parâmetro não é válido.<br/><br/>Se o erro é ParameterMissing ou ParameterInvalidValue, o código de estado HTTP é 400.<br/><br/>Se o erro é HttpNotAllowed, o código de estado HTTP 410.
|RateLimitExceeded||O Bing devolve RateLimitExceeded sempre que excedem suas consultas por segundo (QPS) ou consultas por quota do mês (QPM).<br/><br/>O Bing devolve o código de estado HTTP 429 se excedido QPS e 403 se excedido QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing devolve InvalidAuthorization ao Bing não é possível autenticar o chamador. Por exemplo, o `Ocp-Apim-Subscription-Key` cabeçalho está em falta ou a chave de subscrição não é válida.<br/><br/>Redundância ocorre se especificar mais do que um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de estado HTTP é 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing devolve InsufficientAuthorization quando o chamador não tem permissões para aceder ao recurso. Este erro pode ocorrer se a chave de subscrição foi desativada ou expirou. <br/><br/>Se o erro for InsufficientAuthorization, o código de estado HTTP é 403.

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


## <a name="non-breaking-changes"></a>Alterações sem interrupções  

### <a name="headers"></a>Cabeçalhos

- Adicionado o opcional [Pragma](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) cabeçalho do pedido. Por predefinição, o Bing devolve conteúdo em cache, se disponível. Para impedir que o Bing devolva conteúdo em cache, defina o cabeçalho Pragma como no-cache (por exemplo, Pragma: no-cache).

### <a name="query-parameters"></a>Parâmetros de consulta

- Adicionado o [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) parâmetro de consulta. Utilize este parâmetro para especificar o número de respostas de que pretende que a resposta para incluir. As respostas são escolhidas com base na classificação. Por exemplo, se definir este parâmetro para três (3), a resposta inclui as respostas de classificações três principais.  

- Adicionado o [promover](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) parâmetro de consulta. Utilize este parâmetro juntamente com `answerCount` incluir explicitamente um ou mais tipos de resposta, independentemente da respetiva classificação. Por exemplo, para promover as imagens e vídeos na resposta, definiria promover para *vídeos, imagens*. A lista de respostas de que pretende promover não contam para o `answerCount` limite. Por exemplo, se `answerCount` é 2 e `promote` está definida como *vídeos, imagens*, a resposta pode incluir imagens, vídeos, notícias e páginas da Web.

### <a name="object-changes"></a>Alterações de objetos

- Adicionado o `someResultsRemoved` campo para o [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) objeto. O campo contém um valor booleano que indica se a resposta excluídos alguns resultados a resposta do web.  
