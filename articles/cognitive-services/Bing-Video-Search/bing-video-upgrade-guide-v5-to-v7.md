---
title: Atualizar do Bing v5 de API de pesquisa de vídeos para v7
titlesuffix: Azure Cognitive Services
description: Identifica as partes da sua aplicação que tem de atualizar para utilizar a versão 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 633981682bd8820d72a98b3fc6fbd802e0cd2afb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759899"
---
# <a name="video-search-api-upgrade-guide"></a>Guia de atualização de vídeo a API de pesquisa

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de pesquisa de vídeos do Bing. Utilize este guia para ajudar a identificar as partes da sua aplicação que tem de atualizar para utilizar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- Número da versão do ponto final foi alterado de v5 para v7. Por exemplo, `https://api.cognitive.microsoft.com/bing/v7.0/videos/search`.

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

- Mudar o nome da `modulesRequested` parâmetro de consulta [módulos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Alterações de objetos

- Mudar o nome da `nextOffsetAddCount` campo [vídeos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) para `nextOffset`. A forma como utiliza o deslocamento também foi alterado. Anteriormente, definiria a [desvio](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) parâmetro para consulta o `nextOffset` valor mais o valor de deslocamento anterior mais o número de vídeos no resultado. Agora, basta definir o `offset` consultar o parâmetro para o `nextOffset` valor.  
  
- Alterar o tipo de dados dos `relatedVideos` campo `Video[]` para [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videosmodule) (veja [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videodetails)).

