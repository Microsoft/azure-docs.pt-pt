---
title: Obtenha o estado da tradução
titleSuffix: Azure Cognitive Services
description: O método de estado de tradução obtém o estado de um pedido de tradução documental.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 8b129974396e420948737c9bdf47a5707decab6b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836277"
---
# <a name="get-translation-status"></a>Obtenha o estado da tradução

O método de estado de tradução Get devolve o estado de um pedido de tradução documental. O estatuto inclui o estado geral do pedido e o estado dos documentos que estão a ser traduzidos como parte desse pedido.

## <a name="request-url"></a>URL do Pedido

Envie um `GET` pedido para:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Saiba como encontrar o [nome de domínio personalizado.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **Todos os pedidos da API ao serviço de Tradução documental requerem um ponto final de domínio personalizado.**
> * Não é possível utilizar o ponto final encontrado na página de recursos do portal Azure _Keys e Endpoint,_ nem o ponto final do tradutor `api.cognitive.microsofttranslator.com` global, para fazer pedidos HTTP para Tradução documental.


## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido passados na cadeia de consulta são:

|Parâmetro de consulta|Obrigatório|Descrição|
|--- |--- |--- |
|ID|Verdadeiro|A operação de identificação.|

## <a name="request-headers"></a>Cabeçalhos do pedido

Os cabeçalhos de pedido são:

|Cabeçalhos|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Cabeçalho de pedido exigido|

## <a name="response-status-codes"></a>Códigos de estado de resposta

Seguem-se os possíveis códigos de estado HTTP que um pedido devolve.

|Código de Estado|Description|
|--- |--- |
|200|OK. Pedido bem sucedido e devolve o estado da operação de tradução do lote. HeadersRetry-After: inteiroETag: string|
|401|Não autorizado. Verifique as suas credenciais.|
|404|O recurso não é encontrado.|
|500|Erro interno do servidor.|
|Outros Códigos de Estado|<ul><li>Muitos pedidos</li><li>Servidor temporário indisponível</li></ul>|

## <a name="get-translation-status-response"></a>Obtenha resposta do estado de tradução

### <a name="successful-get-translation-status-response"></a>Obter resposta de estado de tradução bem sucedida

As seguintes informações são devolvidas numa resposta bem sucedida.

|Nome|Tipo|Description|
|--- |--- |--- |
|ID|string|Identificação da operação.|
|createdDateTimeUtc|string|Operação criou a hora da data.|
|últimaActionDateTimeUtc|string|Data em que o estado da operação foi atualizado.|
|status|String|Lista de possíveis estatutos para trabalho ou documento: <ul><li>Cancelado</li><li>Cancelamento</li><li>Com falhas</li><li>Não Começou</li><li>Em Execução</li><li>Com êxito</li><li>ValidaçãoFailed</li></ul>|
|resumo|StatusSummary|Resumo contendo os detalhes listados abaixo.|
|resumo.total|número inteiro|Contagem total.|
|resumo.falhou|número inteiro|Contagem falhada.|
|resumo.sucesso|número inteiro|Número de sucesso.|
|resumo.inProgress|número inteiro|Número em curso.|
|resumo.notYetStarted|número inteiro|O conde de ainda não começou.|
|resumo.cancelado|número inteiro|Número de cancelado.|
|resumo.totalCharacterCharged|número inteiro|Total de caracteres cobrados pela API.|

###<a name="error-response"></a>Resposta de erro

|Nome|Tipo|Description|
|--- |--- |--- |
|code|string|Números contendo códigos de erro de alto nível. Valores possíveis:<br/><ul><li>InternalServerError</li><li>Argumento invalido</li><li>InáduloRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Não autorizado</li></ul>|
|message|string|Recebe uma mensagem de erro de alto nível.|
|alvo|string|Obtém a origem do erro. Por exemplo, seria "documentos" ou "documento de identificação" para um documento inválido.|
|interiorError|InteriorErrorV2|Novo formato de Erro Interior, que está em conformidade com as Diretrizes da API dos Serviços Cognitivos. Contém propriedades necessárias ErrorCode, mensagem e alvo de propriedades opcionais, detalhes (par de valor chave), erro interno (pode ser aninhado).|
|innerError.code|string|Obtém a cadeia de erro de código.|
|innerError.message|string|Recebe uma mensagem de erro de alto nível.|

## <a name="examples"></a>Exemplos

### <a name="example-successful-response"></a>Exemplo de resposta bem sucedida

O seguinte objeto JSON é um exemplo de uma resposta bem sucedida.

```JSON
{
  "id": "727bf148-f327-47a0-9481-abae6362f11e",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Succeeded",
  "summary": {
    "total": 10,
    "failed": 1,
    "success": 9,
    "inProgress": 0,
    "notYetStarted": 0,
    "cancelled": 0,
    "totalCharacterCharged": 0
  }
}
```

### <a name="example-error-response"></a>Resposta de erro de exemplo

O seguinte objeto JSON é um exemplo de uma resposta de erro. O esquema de outros códigos de erro é o mesmo.

Código de estado: 401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>Passos seguintes

Siga o nosso quickstart para saber mais sobre a utilização da Tradução documental e da biblioteca do cliente.

> [!div class="nextstepaction"]
> [Começar com tradução de documentos](../get-started-with-document-translation.md)
