---
title: Upgrade Bing News Search API v5 para v7
titleSuffix: Azure Cognitive Services
description: Identifica as partes da sua aplicação que precisa de atualizar para utilizar a versão 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: bad0ef849af7c94e63f1dfbebda7f47caef9947d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80294368"
---
# <a name="news-search-api-upgrade-guide"></a>Guia de upgrade da API de pesquisa de notícias

Este guia de upgrade identifica as alterações entre a versão 5 e a versão 7 da API de Pesquisa de Notícias Bing. Utilize este guia para o ajudar a identificar as partes da sua aplicação que precisa de atualizar para utilizar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- O número da versão do ponto final mudou de v5 para v7. Por exemplo, `https://api.cognitive.microsoft.com/bing/v7.0/news/search`.

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

### <a name="object-changes"></a>Alterações de objetos

- Adicionei `contractualRules` o campo ao objeto [newsArticle.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) O `contractualRules` campo contém uma lista de regras que deve seguir (por exemplo, atribuição de artigos). Deve aplicar a atribuição fornecida `contractualRules` em `provider`vez de utilizar . O artigo `contractualRules` só inclui quando a resposta [da Web Search API](../bing-web-search/search-the-web.md) contém uma resposta de Notícias.

## <a name="non-breaking-changes"></a>Alterações não-breaking

### <a name="query-parameters"></a>Parâmetros de consulta

- Produtos adicionados como um possível valor para o que pode definir o parâmetro de consulta de [categoria.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) Ver [Categorias Por Mercados.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)

- Acrescentou o parâmetro de consulta [SortBy,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) que devolve tópicos de tendência classificados por data com o primeiro mais recente.

- Acrescentou o parâmetro [Since](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) consulta, que devolve tópicos de tendência que foram descobertos por Bing em ou após o carimbo de tempo especificado da época Unix.

### <a name="object-changes"></a>Alterações de objetos

- Adicionei `mentions` o campo ao objeto [newsArticle.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) O `mentions` campo contém uma lista de entidades (pessoas ou locais) que foram encontradas no artigo.

- Adicionei `video` o campo ao objeto [newsArticle.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) O `video` campo contém um vídeo relacionado com o artigo de notícias. O vídeo ou \<é\> um iframe que pode incorporar ou uma miniatura de movimento.

- Adicionei `sort` o campo ao objeto do [News.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) O `sort` campo mostra a ordem dos artigos. Por exemplo, os artigos são classificados por relevância (padrão) ou data.

- Adicione o objeto [SortValue,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) que define uma ordem de classificação. O `isSelected` campo indica se a resposta usou a ordem de classificação. Se **for verdade,** a resposta usou a ordem de classificação. Se `isSelected` for **falso,** pode utilizar `url` o URL no campo para solicitar uma ordem de classificação diferente.
