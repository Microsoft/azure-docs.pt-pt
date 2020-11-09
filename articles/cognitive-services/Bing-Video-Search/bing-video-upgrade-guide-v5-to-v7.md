---
title: Atualizar Bing Video Search API v5 para v7
titleSuffix: Azure Cognitive Services
description: Identifica as partes da sua aplicação Bing Video Search que precisa de atualizar para utilizar a versão 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 392bb46d481df1f872b9a61beffed9e5b46f4dc5
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379842"
---
# <a name="video-search-api-upgrade-guide"></a>Guia de atualização de API de pesquisa de vídeo

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de Pesquisa de Vídeo Bing. Utilize este guia para o ajudar a identificar as partes da sua aplicação que necessita de atualizar para utilizar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- O número de versão do ponto final mudou de v5 para v7. Por exemplo, `https://api.cognitive.microsoft.com/bing/v7.0/videos/search`.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta a erros e códigos de erro

- Todos os pedidos falhados devem agora incluir um `ErrorResponse` objeto no corpo de resposta.

- Adicionei os seguintes campos ao `Error` objeto.  
  - `subCode`&mdash;Divisórias o código de erro em baldes discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrito no `message` campo
   

- Substituiu os códigos de erro v5 pelos seguintes `code` valores e `subCode` valores possíveis.

|Código|SubCódes|Descrição
|-|-|-
|ServerError|InesperadoError<br/>Recurso<br/>Não ÉDopliizado|Bing devolve ServerError sempre que ocorrer qualquer uma das condições do sub-código. A resposta inclui estes erros se o código de estado HTTP for 500.
|InáduloRequest|ParâmetroSMissing<br/>ParâmetroInvalvalue<br/>HttpNotAllowed<br/>Bloqueado|Bing devolve InvalidRequest sempre que qualquer parte do pedido não seja válida. Por exemplo, falta um parâmetro necessário ou um valor de parâmetro não é válido.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de estado HTTP é 400.<br/><br/>Se o erro for httpNotAllowed, o código de estado HTTP 410.
|TaxaLimitExceed||Bing devoluções TaxaLimitExceed sempre que exceder as suas consultas por segundo (QPS) ou consultas por mês (QPM).<br/><br/>Bing devolve http código de estado 429 se exceder QPS e 403 se excedeu QPM.
|InvalidAuthorization|AutorizaçãoSDissing<br/>AutorizaçãoSAundação|Bing devolve invalidAuthorization quando Bing não pode autenticar o chamador. Por exemplo, falta o `Ocp-Apim-Subscription-Key` cabeçalho ou a chave de subscrição não é válida.<br/><br/>A redundância ocorre se especificar mais de um método de autenticação.<br/><br/>Se o erro for invalidauthorization, o código de estado HTTP é 401.
|Authorização insuficiente|AutorizaçãoDisabled<br/>AutorizaçãoSDusa|Bing devolve aautorização insuficiente quando o chamador não tem permissões para aceder ao recurso. Isto pode ocorrer se a chave de subscrição tiver sido desativada ou tiver expirado. <br/><br/>Se o erro for insuficiente, o código de estado HTTP é 403.

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

### <a name="query-parameters"></a>Parâmetros de consulta

- Renomeou o `modulesRequested` parâmetro de consulta para [módulos.](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested)  

### <a name="object-changes"></a>Alterações de objetos

- Renomeado o `nextOffsetAddCount` campo de [Vídeos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) para `nextOffset` . A forma como usas o offset também mudou. Anteriormente, definiria o parâmetro de consulta [de compensação](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) para o `nextOffset` valor mais o valor de compensação anterior mais o número de vídeos no resultado. Agora, basta definir o `offset` parâmetro de consulta para o `nextOffset` valor.  
  
- Alterou o tipo de dados `relatedVideos` do campo de para `Video[]` [VideosModule](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videosmodule) (ver [VideoDetails).](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails)