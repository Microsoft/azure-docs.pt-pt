---
title: Upgrade de API v5 para v7 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Determine quais as partes da sua aplicação que requerem atualizações para utilizar as APIs de Pesquisa Web bing.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881311"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Upgrade de Bing Web Search API v5 para v7

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de Pesquisa Web bing. Utilize este guia para o ajudar a identificar as partes da sua aplicação que precisa de atualizar para utilizar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- O número da versão final mudou de v5 para v7. Por exemplo,\/\/https: api.cognitive.microsoft.com/bing/**v7.0**/search.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta a erros e códigos de erro

- Todos os pedidos falhados `ErrorResponse` devem agora incluir um objeto no corpo de resposta.

- Adicione os seguintes `Error` campos ao objeto.  
  - `subCode`&mdash;Divisórias o código de erro em baldes discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre `message` o erro descrito no campo


- Substituiu os códigos de erro `code` `subCode` v5 com os seguintes valores possíveis e valores.

|Código|Subcódigo|Descrição
|-|-|-
|Error do servidor|Erro Inesperado<br/>Erro de Recursos<br/>Não Implementado|Bing devolve ServerError sempre que ocorrer em qualquer uma das condições do subcódigo. A resposta incluirá estes erros se o código de estado HTTP for de 500.
|Pedido inválido|Parâmetros desaparecidos<br/>ParâmetroSInvalidValue<br/>Httpnotado<br/>Bloqueado|Devoluções Bing InvalidRequest sempre que qualquer parte do pedido não for válida. Por exemplo, falta um parâmetro necessário ou não é válido um valor de parâmetro.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de estado HTTP é de 400.<br/><br/>Se o erro for httpNotAllowed, o código de estado HTTP 410.
|RateLimitExceeded||Bing devolve RateLimitExceeded sempre que excede as suas consultas por segundo (QPS) ou consultas por mês (QPM) quota.<br/><br/>Bing devolve o código de estado HTTP 429 se exceder qPS e 403 se exceder o QPM.
|Autorização Inválida|Autorização Faltando<br/>AutorizaçãoRedundy|Bing devolve InvalidAuthorization quando Bing não pode autenticar o chamador. Por exemplo, `Ocp-Apim-Subscription-Key` falta o cabeçalho ou a chave de subscrição não é válida.<br/><br/>O despedimento ocorre se especificar mais do que um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de estado HTTP é 401.
|Autorização Insuficiente|AutorizaçãoDeficiente<br/>Autorizações Caducadas|Bing devolve InsuficienteAutorização quando o chamador não tem permissões para aceder ao recurso. Este erro pode ocorrer se a chave de subscrição tiver sido desativada ou tiver expirado. <br/><br/>Se o erro for insuficiente, o código de estado HTTP é 403.

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


## <a name="non-breaking-changes"></a>Alterações não-destantes  

### <a name="headers"></a>Cabeçalhos

- Acrescentou o cabeçalho opcional do pedido [pragma.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) Por predefinição, o Bing devolve conteúdo em cache, se disponível. Para impedir que o Bing devolva conteúdo em cache, defina o cabeçalho Pragma como no-cache (por exemplo, Pragma: no-cache).

### <a name="query-parameters"></a>Parâmetros de consulta

- Acrescentou o parâmetro de consulta [do Conde.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) Utilize este parâmetro para especificar o número de respostas que pretende que a resposta inclua. As respostas são escolhidas com base no ranking. Por exemplo, se definir este parâmetro para três (3), a resposta inclui as três respostas mais bem classificadas.  

- Acrescentou o parâmetro de consulta [de promoção.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) Utilize este parâmetro `answerCount` juntamente com um ou mais tipos de resposta, independentemente do seu ranking. Por exemplo, para promover vídeos e imagens na resposta, definiria a promoção de *vídeos, imagens.* A lista de respostas que pretende promover `answerCount` não conta contra o limite. Por exemplo, `answerCount` se `promote` for 2 e for definido para *vídeos,imagens*, a resposta pode incluir páginas web, notícias, vídeos e imagens.

### <a name="object-changes"></a>Alterações de objetos

- Adicione `someResultsRemoved` o campo ao objeto [WebAnswer.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) O campo contém um valor Boolean o que indica se a resposta excluiu alguns resultados da resposta web.  
