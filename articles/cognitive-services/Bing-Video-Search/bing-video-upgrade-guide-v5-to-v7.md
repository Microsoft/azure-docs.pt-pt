---
title: Upgrade Bing Video Search API v5 para v7
titleSuffix: Azure Cognitive Services
description: Identifica as partes da sua aplicação que precisa de atualizar para utilizar a versão 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 5dc4c870ae8dbe9f082456d738836aced1271732
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68500735"
---
# <a name="video-search-api-upgrade-guide"></a>Guia de upgrade da API de pesquisa de vídeo

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API bing video search. Utilize este guia para o ajudar a identificar as partes da sua aplicação que precisa de atualizar para utilizar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- O número da versão do ponto final mudou de v5 para v7. Por exemplo, `https://api.cognitive.microsoft.com/bing/v7.0/videos/search`.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta a erros e códigos de erro

- Todos os pedidos falhados `ErrorResponse` devem agora incluir um objeto no corpo de resposta.

- Adicione os seguintes `Error` campos ao objeto.  
  - `subCode`&mdash;Divisórias o código de erro em baldes discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre `message` o erro descrito no campo
   

- Substituiu os códigos de erro `code` `subCode` v5 com os seguintes valores possíveis e valores.

|Código|Subcódigo|Descrição
|-|-|-
|Error do servidor|Erro Inesperado<br/>Erro de Recursos<br/>Não Implementado|Bing devolve ServerError sempre que ocorrer em qualquer uma das condições do subcódigo. A resposta inclui estes erros se o código de estado HTTP for de 500.
|Pedido inválido|Parâmetros desaparecidos<br/>ParâmetroSInvalidValue<br/>Httpnotado<br/>Bloqueado|Devoluções Bing InvalidRequest sempre que qualquer parte do pedido não for válida. Por exemplo, falta um parâmetro necessário ou não é válido um valor de parâmetro.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de estado HTTP é de 400.<br/><br/>Se o erro for httpNotAllowed, o código de estado HTTP 410.
|RateLimitExceeded||Bing devolve RateLimitExceeded sempre que excede as suas consultas por segundo (QPS) ou consultas por mês (QPM) quota.<br/><br/>Bing devolve o código de estado HTTP 429 se exceder qPS e 403 se exceder o QPM.
|Autorização Inválida|Autorização Faltando<br/>AutorizaçãoRedundy|Bing devolve InvalidAuthorization quando Bing não pode autenticar o chamador. Por exemplo, `Ocp-Apim-Subscription-Key` falta o cabeçalho ou a chave de subscrição não é válida.<br/><br/>O despedimento ocorre se especificar mais do que um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de estado HTTP é 401.
|Autorização Insuficiente|AutorizaçãoDeficiente<br/>Autorizações Caducadas|Bing devolve InsuficienteAutorização quando o chamador não tem permissões para aceder ao recurso. Isto pode ocorrer se a chave de subscrição tiver sido desativada ou tiver expirado. <br/><br/>Se o erro for insuficiente, o código de estado HTTP é 403.

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

### <a name="query-parameters"></a>Parâmetros de consulta

- Renomeado `modulesRequested` o parâmetro de consulta para [módulos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Alterações de objetos

- Renomeado `nextOffsetAddCount` o campo `nextOffset`de [Vídeos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) para . A forma como usas a compensação também mudou. Anteriormente, definiria [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) o parâmetro de `nextOffset` consulta offset para o valor mais o valor de compensação anterior mais o número de vídeos no resultado. Agora, basta definir `offset` o parâmetro de `nextOffset` consulta para o valor.  
  
- Alterou o tipo `relatedVideos` de `Video[]` dados do campo para [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videosmodule) (ver [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails)).

