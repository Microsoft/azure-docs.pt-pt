---
title: Upgrade Bing Spell Check API v5 para v7
titleSuffix: Azure Cognitive Services
description: Identifica as partes da sua aplicação que precisa de atualizar para utilizar a versão 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 545772a28a67310b12eb55cd1fb14e8d12a95a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68500823"
---
# <a name="spell-check-api-upgrade-guide"></a>Guia de upgrade da API de verificação de feitiços

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API bing spell check. Utilize este guia para o ajudar a identificar as partes da sua aplicação que precisa de atualizar para utilizar a versão 7.

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="endpoints"></a>Pontos Finais

- O número da versão do ponto final mudou de v5 para v7. Por exemplo, `https://api.cognitive.microsoft.com/bing/v7.0/spellcheck`.

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
|Autorização Insuficiente|AutorizaçãoDeficiente<br/>Autorizações Caducadas|Bing devolve InsuficienteAutorização quando o chamador não tem permissão para aceder ao recurso. Isto pode ocorrer se a chave de subscrição tiver sido desativada ou tiver expirado. <br/><br/>Se o erro for insuficiente, o código de estado HTTP é 403.

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Requisitos de utilização e exibição](./UseAndDisplayRequirements.md)
