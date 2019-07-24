---
title: Atualizar API de Sugestão Automática do Bing V5 para v7
titleSuffix: Azure Cognitive Services
description: Identifica as partes do seu aplicativo que você precisa atualizar para usar a versão 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 5249a3a1f51eea2ecd0999d71c6b08fdacf37a34
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405410"
---
# <a name="autosuggest-api-upgrade-guide"></a>Guia de atualização de API de sugestão automática

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 do API de Sugestão Automática do Bing. Use este guia para ajudar a atualizar seu aplicativo para usar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- O número de versão do ponto de extremidade foi alterado de V5 para v7. Por exemplo, https:\//API.cognitive.Microsoft.com/Bing/\*\*v 7.0 * */suggestions.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta de erro e códigos de erro

- Todas as solicitações com falha agora devem `ErrorResponse` incluir um objeto no corpo da resposta.

- Foram adicionados os campos a seguir `Error` ao objeto.  
  - `subCode`&mdash;Particiona o código de erro em buckets discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrito no `message` campo

- Substituídos os códigos de erro V5 pelos seguintes `code` valores `subCode` e possíveis.

|Código|Subcódigo|Descrição
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|O Bing retorna ServerError sempre que qualquer uma das condições de subcódigo ocorrerem. A resposta incluirá esses erros se o código de status HTTP for 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing retorna InvalidRequest sempre que qualquer parte da solicitação não é válida. Por exemplo, um parâmetro obrigatório está ausente ou um valor de parâmetro não é válido.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de status HTTP será 400.<br/><br/>Se o erro for HttpNotAllowed, o código de status HTTP será 410.
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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Requisitos de utilização e de apresentação](./UseAndDisplayRequirements.md)
