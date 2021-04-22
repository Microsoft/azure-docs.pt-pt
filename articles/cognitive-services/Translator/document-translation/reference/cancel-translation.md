---
title: Cancelar método de tradução
titleSuffix: Azure Cognitive Services
description: O método de tradução cancela uma operação atualmente de processamento ou fila.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: e3b7da30f54b9d9468b46a2cd0972a3397e5cdce
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865112"
---
# <a name="cancel-translation"></a>Cancelar tradução

Cancelar uma operação de processamento ou fila atual. Uma operação não será cancelada se já estiver concluída ou falhada ou cancelada. Um mau pedido será devolvido. Todos os documentos que tenham concluído a tradução não serão cancelados e serão cobrados. Todos os documentos pendentes serão cancelados, se possível.

## <a name="request-url"></a>URL do Pedido

Envie um `DELETE` pedido para:

```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Saiba como encontrar o [nome de domínio personalizado.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **Todos os pedidos da API ao serviço de Tradução documental requerem um ponto final de domínio personalizado.**
> * Não é possível utilizar o ponto final encontrado na página de recursos do portal Azure _Keys e Endpoint,_ nem o ponto final do tradutor `api.cognitive.microsofttranslator.com` global, para fazer pedidos HTTP para Tradução documental.

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido passados na cadeia de consulta são:

|Parâmetro de consulta|Obrigatório|Descrição|
|-----|-----|-----|
|ID|Verdadeiro|A operação id.|

## <a name="request-headers"></a>Cabeçalhos do pedido

Os cabeçalhos de pedido são:

|Cabeçalhos|Description|
|-----|-----|
|Ocp-Apim-Subscription-Key|Cabeçalho de pedido exigido|

## <a name="response-status-codes"></a>Códigos de estado de resposta

Seguem-se os possíveis códigos de estado HTTP que um pedido devolve.

| Código de Estado| Description|
|-----|-----|
|200|OK. Pedido de cancelamento foi submetido|
|401|Não autorizado. Verifique as suas credenciais.|
|404|Não encontrado. O recurso não é encontrado. 
|500|Erro interno do servidor.
|Outros Códigos de Estado|<ul><li>Muitos pedidos</li><li>Servidor temporário indisponível</li></ul>|

## <a name="cancel-translation-response"></a>Cancelar resposta de tradução

### <a name="successful-response"></a>Resposta bem sucedida

As seguintes informações são devolvidas numa resposta bem sucedida.

|Nome|Tipo|Description|
|--- |--- |--- |
|ID|string|Identificação da operação.|
|createdDateTimeUtc|string|Operação criou a hora da data.|
|últimaActionDateTimeUtc|string|Data em que o estado da operação foi atualizado.|
|status|String|Lista de possíveis estatutos para trabalho ou documento: <ul><li>Cancelado</li><li>Cancelamento</li><li>Com falhas</li><li>Não Começou</li><li>Em Execução</li><li>Com êxito</li><li>ValidaçãoFailed</li></ul>|
|resumo|StatusSummary|Resumo contendo os detalhes listados abaixo.|
|resumo.total|número inteiro|Contagem de documentos totais.|
|resumo.falhou|número inteiro|A contagem de documentos falhou.|
|resumo.sucesso|número inteiro|Contagem de documentos traduzidos com sucesso.|
|resumo.inProgress|número inteiro|Contagem de documentos em curso.|
|resumo.notYetStarted|número inteiro|A contagem de documentos ainda não começou a ser processada.|
|resumo.cancelado|número inteiro|Número de cancelado.|
|resumo.totalCharacterCharged|número inteiro|Total de caracteres cobrados pela API.|

### <a name="error-response"></a>Resposta de erro

|Nome|Tipo|Description|
|--- |--- |--- |
|code|string|Números contendo códigos de erro de alto nível. Valores possíveis:<br/><ul><li>InternalServerError</li><li>Argumento invalido</li><li>InáduloRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Não autorizado</li></ul>|
|message|string|Recebe uma mensagem de erro de alto nível.|
|alvo|string|Obtém a origem do erro. Por exemplo, seria "documentos" ou "documento de identificação" para um documento inválido.|
|interiorError|InteriorErrorV2|Novo formato de Erro Interior, que está em conformidade com as Diretrizes da API dos Serviços Cognitivos. Contém propriedades necessárias ErrorCode, mensagem e alvo de propriedades opcionais, detalhes (par de valor chave), erro interno (pode ser aninhado).|
|innerError.code|string|Obtém a cadeia de erro de código.|
|interior. Eroor.mensagem|string|Recebe uma mensagem de erro de alto nível.|

## <a name="examples"></a>Exemplos

### <a name="example-successful-response"></a>Exemplo de resposta bem sucedida

O seguinte objeto JSON é um exemplo de uma resposta bem sucedida.

Código de estado: 200

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

Código de estado: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Passos seguintes

Siga o nosso quickstart para saber mais sobre a utilização da Tradução documental e da biblioteca do cliente.

> [!div class="nextstepaction"]
> [Começar com tradução de documentos](../get-started-with-document-translation.md)
