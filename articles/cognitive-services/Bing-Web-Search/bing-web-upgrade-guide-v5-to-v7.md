---
title: Upgrade de API v5 para v7 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Determine quais as partes da sua aplicação que requerem atualizações para utilizar as APIs de Pesquisa Web Bing v7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d930543671a5328d76a38aa7e1b421c111e89e39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349525"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Upgrade de Bing Web Search API v5 para v7

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de Pesquisa Web Bing. Utilize este guia para o ajudar a identificar as partes da sua aplicação que necessita de atualizar para utilizar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- O número de versão do ponto final passou de v5 para v7. Por exemplo, https: \/ \/ api.cognitive.microsoft.com/bing/**v7.0**/search.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta a erros e códigos de erro

- Todos os pedidos falhados devem agora incluir um `ErrorResponse` objeto no corpo de resposta.

- Adicionei os seguintes campos ao `Error` objeto.  
  - `subCode`&mdash;Divisórias o código de erro em baldes discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrito no `message` campo


- Substituiu os códigos de erro v5 pelos seguintes `code` valores e `subCode` valores possíveis.

|Código|SubCódes|Description
|-|-|-
|ServerError|InesperadoError<br/>Recurso<br/>Não ÉDopliizado|Bing devolve ServerError sempre que ocorrer qualquer uma das condições de subcodições. A resposta incluirá estes erros se o código de estado HTTP for 500.
|InáduloRequest|ParâmetroSMissing<br/>ParâmetroInvalvalue<br/>HttpNotAllowed<br/>Bloqueado|Bing devolve InvalidRequest sempre que qualquer parte do pedido não seja válida. Por exemplo, falta um parâmetro necessário ou um valor de parâmetro não é válido.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de estado HTTP é 400.<br/><br/>Se o erro for httpNotAllowed, o código de estado HTTP 410.
|TaxaLimitExceed||Bing devoluções TaxaLimitExceed sempre que exceder as suas consultas por segundo (QPS) ou consultas por mês (QPM).<br/><br/>Bing devolve http código de estado 429 se exceder QPS e 403 se excedeu QPM.
|InvalidAuthorization|AutorizaçãoSDissing<br/>AutorizaçãoSAundação|Bing devolve invalidAuthorization quando Bing não pode autenticar o chamador. Por exemplo, falta o `Ocp-Apim-Subscription-Key` cabeçalho ou a chave de subscrição não é válida.<br/><br/>A redundância ocorre se especificar mais de um método de autenticação.<br/><br/>Se o erro for invalidauthorization, o código de estado HTTP é 401.
|Authorização insuficiente|AutorizaçãoDisabled<br/>AutorizaçãoSDusa|Bing devolve aautorização insuficiente quando o chamador não tem permissões para aceder ao recurso. Este erro pode ocorrer se a chave de subscrição tiver sido desativada ou tiver expirado. <br/><br/>Se o erro for insuficiente, o código de estado HTTP é 403.

- Os seguintes mapas dos códigos de erro anteriores para os novos códigos. Se tiver assumido uma dependência dos códigos de erro v5, atualize o seu código em conformidade.

|Código da versão 5|Versão 7 código.subCode
|-|-
|PedidoParameterMissing|InvalidRequest.ParameterMissing
PedidoParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|Authorização insuficiente
ExcedidoVolume|TaxaLimitExceed
UltrapassadoQpsLimit|TaxaLimitExceed
Desativado|Authorização insuficiente.AutorizaçãoDisabled
InesperadoError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AutorizaçãoSDissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.httpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Não ÉDopliizado|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ValidadeAuthorizationToken|Authorization insuficiente.AutorizaçãoExpired
Ómuiso insuficiente|Authorização insuficiente
Bloqueado|InvalidRequest.Bloqueado


## <a name="non-breaking-changes"></a>Alterações não-quebrando  

### <a name="headers"></a>Cabeçalhos

- Acrescentou o cabeçalho de pedido [de Pragma](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) opcional. Por predefinição, o Bing devolve conteúdo em cache, se disponível. Para impedir que o Bing devolva conteúdo em cache, defina o cabeçalho Pragma como no-cache (por exemplo, Pragma: no-cache).

### <a name="query-parameters"></a>Parâmetros de consulta

- Adicionei o parâmetro de consulta [answerCount.](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) Utilize este parâmetro para especificar o número de respostas que pretende que a resposta inclua. As respostas são escolhidas com base no ranking. Por exemplo, se definir este parâmetro para três (3), a resposta inclui as três melhores respostas classificadas.  

- Acrescentou o parâmetro de consulta [de promoção.](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) Utilize este parâmetro juntamente com `answerCount` para incluir explicitamente um ou mais tipos de resposta, independentemente do seu ranking. Por exemplo, para promover vídeos e imagens na resposta, definiria promover *para vídeos, imagens*. A lista de respostas que pretende promover não conta contra o `answerCount` limite. Por exemplo, se `answerCount` for 2 e `promote` estiver definido para *vídeos, imagens,* a resposta pode incluir páginas web, notícias, vídeos e imagens.

### <a name="object-changes"></a>Alterações de objetos

- Adicionei o `someResultsRemoved` campo ao objeto [WebAnswer.](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) O campo contém um valor Boolean que indica se a resposta excluiu alguns resultados da resposta web.