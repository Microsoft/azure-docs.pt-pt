---
title: Atualizar Bing News Search API v5 para v7
titleSuffix: Azure Cognitive Services
description: Identifica as partes da sua aplicação Bing News Search que precisa de atualizar para utilizar a versão 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: a114cb24d79189f9e370fae1962f60ca97241d90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351372"
---
# <a name="news-search-api-upgrade-guide"></a>Guia de atualização de API de pesquisa de notícias

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de Pesquisa de Notícias Bing. Utilize este guia para o ajudar a identificar as partes da sua aplicação que necessita de atualizar para utilizar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- O número de versão do ponto final mudou de v5 para v7. Por exemplo, `https://api.cognitive.microsoft.com/bing/v7.0/news/search`.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta a erros e códigos de erro

- Todos os pedidos falhados devem agora incluir um `ErrorResponse` objeto no corpo de resposta.

- Adicionei os seguintes campos ao `Error` objeto.  
  - `subCode`&mdash;Divisórias o código de erro em baldes discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrito no `message` campo

- Substituiu os códigos de erro v5 pelos seguintes `code` valores e `subCode` valores possíveis.

|Código|SubCódes|Description
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

### <a name="object-changes"></a>Alterações de objetos

- Adicionei o `contractualRules` campo ao objeto [NewsArticle.](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) O `contractualRules` campo contém uma lista de regras que deve seguir (por exemplo, atribuição de artigos). Deve aplicar a atribuição fornecida `contractualRules` em vez de utilizar `provider` . O artigo só inclui `contractualRules` quando a resposta da [API de Pesquisa na Web](../bing-web-search/overview.md) contém uma resposta news.

## <a name="non-breaking-changes"></a>Alterações não-quebrando

### <a name="query-parameters"></a>Parâmetros de consulta

- Produtos adicionados como um possível valor para o quais poderá definir o parâmetro de consulta de [categoria.](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) Ver [Categorias Por Mercados.](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)

- Adicionei o parâmetro de consulta [SortBy,](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) que retorna tópicos de tendência classificados por data com o mais recente primeiro.

- Acrescentou o parâmetro [de consulta Desde,](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) que devolve tópicos de tendência que foram descobertos por Bing sobre ou após o tempotamp de época unix especificado.

### <a name="object-changes"></a>Alterações de objetos

- Adicionei o `mentions` campo ao objeto [NewsArticle.](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) O `mentions` campo contém uma lista de entidades (pessoas ou lugares) que foram encontradas no artigo.

- Adicionei o `video` campo ao objeto [NewsArticle.](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) O `video` campo contém um vídeo relacionado com o artigo de notícias. O vídeo é um \<iframe\> que pode incorporar ou uma miniatura de movimento.

- Adicionei o `sort` campo ao objeto [News.](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) O `sort` campo mostra a ordem dos artigos. Por exemplo, os artigos são classificados por relevância (padrão) ou data.

- Adicionei o objeto [SortValue,](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) que define uma ordem de classificação. O `isSelected` campo indica se a resposta usou a ordem de classificação. Se **for verdade,** a resposta usou a ordem de ordem. Se `isSelected` for **falso,** pode utilizar o URL no `url` campo para solicitar uma ordem de classificação diferente.